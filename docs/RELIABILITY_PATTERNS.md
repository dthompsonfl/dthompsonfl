# Reliability Patterns

This document describes reliability patterns I use when designing operational software where failures can affect payments, orders, employee workflows, customer experience, financial state, or other business-critical behavior.

The exact implementation depends on the system. These are engineering patterns, not a claim that every project uses every pattern identically.

## Public implementation evidence

The strongest public examples currently live in [dthompsonfl/pos](https://github.com/dthompsonfl/pos).

Useful direct references:

- [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) — Room-backed outbox state, retry metadata, unique idempotency-key index
- [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) — WorkManager drain/retry/conflict handling
- [PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt) — explicit mobile schema migrations
- [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) — authenticated payment operations, scoped idempotency, context checks, webhook signature verification
- [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) — simulated-versus-real behavior and fail-closed production bridge requirement
- [SyncEventStore.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/storage/SyncEventStore.kt) — useful sync-state model plus an explicit warning that the current backend store is in-memory development infrastructure
- [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md) — repository verification and remaining external-validation boundary

A second public example is [ECCB's Redis/BullMQ queue implementation](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts), which demonstrates queue initialization, worker concurrency, retry exhaustion handling, a dead-letter queue, and graceful shutdown.

---

## 1. Idempotent commands

A side-effecting command should be safe when the same logical request is delivered more than once.

Typical examples:

- payment creation/capture
- refund initiation
- order submission
- kitchen fire
- inventory adjustment
- shift close
- external integration commands

A useful model is:

~~~text
command
  |
  v
derive stable business operation key
  |
  v
claim operation atomically
  |
  +--> prior success ------> return recorded result
  |
  +--> prior in-progress --> observe/return current state
  |
  +--> new ---------------> execute and persist outcome
~~~

The key should represent the **business operation**, not merely a random HTTP request.

### Public evidence and nuance

[PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) scopes a client-provided idempotency key by merchant/store/register before provider calls.

If the caller omits the key, the current route generates a UUID. That guarantees uniqueness but does **not** make a later independent retry the same logical operation. Production callers therefore need to generate and reuse stable keys for operations that must deduplicate.

---

## 2. Idempotency races

This implementation is unsafe:

~~~text
if idempotency_key not found:
    perform side effect
    store idempotency_key
~~~

Two concurrent requests can both observe “not found.”

Production-safe approaches may include:

- a unique database constraint
- atomic insert/claim
- transaction boundaries
- compare-and-set semantics
- serialized command processing
- provider idempotency plus local durable result persistence

The guarantee should be inspectable at the storage/transaction layer, not inferred from application-level checks.

### Public evidence boundary

The public POS [SyncEventStore.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/storage/SyncEventStore.kt) is explicitly an in-memory development store and documents the production need for a database-backed unique constraint. I treat it as design evidence, not as production atomicity proof.

---

## 3. Explicit state machines

If a workflow has meaningful stages, I prefer to model them explicitly.

Instead of:

~~~text
status = arbitrary string
~~~

prefer something closer to:

~~~text
DRAFT
  -> SUBMITTED
  -> PROCESSING
      -> COMPLETED
      -> FAILED_RETRYABLE
      -> FAILED_FINAL
      -> RECONCILIATION_REQUIRED
~~~

Benefits:

- invalid transitions can be rejected;
- retries become easier to reason about;
- stale clients can be detected;
- UI can represent uncertainty honestly;
- audit history has meaning;
- recovery starts from a known state.

---

## 4. Server-authoritative transitions

Clients request transitions. They should not independently declare business-critical state.

Example:

~~~text
client: request "capture payment"

server:
  - resolve identity
  - evaluate permission
  - validate tenant/location/register scope
  - validate current payment/order state
  - validate operation identity
  - execute provider/local transition
  - persist result and audit context

client:
  - render authoritative result
~~~

This reduces drift between web, mobile, POS, KDS, admin, and background workers.

---

## 5. Retry taxonomy

Not every failure should retry.

### Retryable

Examples:

- temporary network failure before side-effect certainty
- rate limiting
- transient infrastructure failure
- selected 5xx responses
- provider timeout when stable idempotency makes a retry safe

### Permanent / non-retryable

Examples:

- validation failure
- permission denial
- invalid state transition
- malformed external payload
- unsupported operation
- stale revision requiring conflict resolution

### Unknown outcome / reconciliation required

Examples:

- provider may have completed the operation but the local system did not receive a definitive response;
- timeout occurred after an irreversible request was sent;
- local state and provider state disagree;
- synchronous response and webhook disagree.

Blind retries are dangerous when money or irreversible state is involved.

---

## 6. Reconciliation-required states

“Unknown” is a real state.

A robust workflow preserves enough evidence to answer:

1. What operation did we intend?
2. What stable operation identity was used?
3. Was the external request dispatched?
4. What response, if any, was received?
5. What was persisted locally?
6. What does the external provider report now?
7. Can the operation safely be retried?
8. Does a human need to decide?

A reconciliation queue is often more correct than pretending a timeout means failure.

---

## 7. Durable outbox

When a local state change and later external dispatch must stay logically connected, a durable outbox avoids this failure:

~~~text
database commit succeeds
event publish fails
downstream never sees the change
~~~

A common pattern:

~~~text
transaction:
  update business state
  insert outbox event

worker:
  claim event
  dispatch
  record delivery state
  retry/reconcile under policy
~~~

Consumers still need idempotency because at-least-once delivery can duplicate events.

### Public evidence

[SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) models a durable Room outbox; [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) drains it with WorkManager.

The public backend remains a separate concern: its development sync store is not yet durable production persistence.

---

## 8. Idempotent consumers

A durable outbox does not guarantee exactly-once delivery.

A consumer should be prepared to receive the same event more than once.

Useful techniques:

- unique consumed-event key
- natural-key upsert
- conditional transition
- compare-and-set version
- transactionally persisted processed-event record

The correct technique depends on whether the consumer applies a state snapshot, state transition, financial side effect, or append-only fact.

---

## 9. Webhooks

Webhooks are untrusted, replayable external input.

Controls should include, where supported:

- signature verification
- timestamp/tolerance checks
- replay protection
- event deduplication
- schema validation
- provider/account mapping
- local resource mapping
- idempotent processing
- durable processing state
- observable failures
- provider reconciliation

A webhook should not bypass local invariants just because it came from a trusted provider.

### Public evidence

[PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) verifies Stripe webhook signatures and keeps an in-process duplicate-event map.

### Limitation

The current duplicate-event map is process memory. It is not durable deduplication across restarts or multiple instances. The handler also delegates real reconciliation work conceptually rather than proving a complete durable reconciliation worker in public source.

---

## 10. Optimistic UI boundaries

Optimistic interfaces can improve responsiveness, but they should not fabricate success for consequential operations.

### Usually reasonable

- navigation
- local selection
- draft editing
- reversible preferences
- low-risk local display state

### Require authoritative confirmation

- payment success
- refund completion
- permission changes
- irreversible fulfillment transitions
- financial reconciliation
- manager overrides

An optimistic UI should know what it is allowed to speculate about.

---

## 11. Offline behavior

Offline behavior should be classified by risk.

### Locally safe

Examples:

- cached read models
- draft order editing
- local notes
- nonauthoritative navigation state

### Queueable

Examples:

- selected mutations with durable local persistence
- stable command identity
- deterministic replay
- conflict semantics

### Must fail closed

Examples:

- payment capture without supported offline semantics
- permission-sensitive override
- operation requiring fresh authority
- irreversible external side effect when outcome cannot be safely inferred

### Unknown outcome

A provider request that timed out after dispatch should not silently become a “failed” operation eligible for blind retry.

### Public evidence

The POS repository provides Room-backed local persistence plus WorkManager retry/conflict handling.

Private restaurant/Android source additionally contains explicit offline capability classes and outbox behavior. That source is summarized here without publishing private implementation.

---

## 12. Fail-closed integrations

An unavailable production integration should not silently become fake success.

Examples:

- no real payment bridge -> card-present path unavailable
- missing webhook secret -> webhook endpoint rejects or reports unavailable
- no hardware transport -> device action reports unavailable
- missing authorization context -> protected command denied
- missing required release signing -> release gate fails

### Public evidence

[StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) explicitly separates simulated development behavior from real mode and returns a configuration error when the real bridge is not installed.

---

## 13. Concurrency and race conditions

Correctness under one request does not prove correctness under concurrent requests.

Questions I expect to answer:

- Can two terminals update the same order?
- Can two workers claim the same job?
- Can two payment attempts race for one logical operation?
- Can stale clients overwrite a newer revision?
- Can inventory be decremented below zero?
- Can a shift close while a transaction is committing?
- Can two managers apply conflicting overrides?
- Can webhook and synchronous completion race?

Controls may include:

- database constraints
- optimistic version columns
- row/advisory locks where appropriate
- compare-and-set transitions
- transaction isolation
- idempotent consumers
- serialized processing for selected aggregates

---

## 14. Stale clients and revisions

Long-lived clients are normal in operational systems.

A mutation can include an expected revision/version:

~~~text
update order
where id = ?
  and version = expected_version
~~~

If zero rows change, the client is stale.

The response should not merely say “500.” It should return a conflict the user or client can resolve by refreshing, merging, or explicitly overriding under policy.

---

## 15. Auditability

For high-consequence changes, useful audit context can include:

- actor
- delegated/approving actor
- timestamp
- source surface/device
- tenant/location/register
- prior state
- command
- resulting state
- reason/override reason
- correlation/operation ID
- provider reference
- request/revision context

Audit history should be designed, not reconstructed later from generic logs.

---

## 16. Business-level observability

Infrastructure metrics are necessary but insufficient.

Useful operational questions include:

- how many orders are stuck in nonterminal state?
- how many payments require reconciliation?
- how many duplicate commands were deduplicated?
- what is the outbox backlog per device/location?
- how old is the oldest queued mutation?
- which provider is producing retries/timeouts?
- how long do tickets remain in each kitchen state?
- how many manager overrides occur?
- how many shifts cannot reconcile?
- how stale is menu/catalog synchronization?

Observability should connect technical state to business impact.

---

## 17. Timeouts and deadlines

Every external call needs a deliberate deadline.

Questions include:

- how long can the user wait?
- can the operation continue asynchronously?
- can cancellation propagate?
- is retry safe?
- what state remains after timeout?
- does the caller know whether the external side effect occurred?

A timeout is a transport observation, not automatically a business outcome.

---

## 18. Backpressure and overload

Systems should degrade intentionally.

Controls may include:

- queue depth limits
- worker concurrency caps
- rate limits
- admission control
- circuit breakers
- provider-specific throttles
- priority queues
- dropping/deferment of noncritical telemetry

During overload, revenue and safety-critical workflows should not compete equally with low-value background tasks.

### Public queue evidence

[ECCB's BullMQ queue layer](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts) exposes named queues, configurable worker concurrency, retries, queue stats, and dead-letter handling. This is general queue implementation evidence, not a claim about restaurant production load.

---

## 19. Schema and migration safety

Schema changes are runtime changes.

Migration planning should account for:

- backward compatibility
- app-version skew
- large-table changes
- nullability/default changes
- index creation cost
- data backfills
- destructive changes
- rollback or roll-forward strategy
- offline/mobile schema versions
- deployment ordering

### Public evidence

[PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt) provides explicit Room migrations for the POS client.

Private PostgreSQL systems contain additional migration and schema-validation controls; those are summarized in the portfolio without publishing private source.

---

## 20. Authorization is part of correctness

An operation that succeeds for the wrong actor is not reliable.

For sensitive workflows, correctness includes:

- authentication
- server-side authorization
- tenant/resource scope
- location/register scope
- input validation
- secret isolation
- provider verification
- auditability

A hidden button is not an authorization control.

---

## 21. Recovery design

A production workflow is incomplete if it only describes success.

For every important operation I try to answer:

1. What can fail?
2. What state remains after each failure?
3. Can the operation be retried safely?
4. How do we know whether an external side effect happened?
5. What does the user/operator see?
6. What can recover automatically?
7. When is human intervention required?
8. Is the final state auditable?
9. Can the system explain what happened later?
10. What is the rollback or compensating action?

---

## 22. Failure-oriented testing

High-value tests attack assumptions.

Examples:

- duplicate command
- concurrent command
- stale revision
- crash between external success and local commit
- webhook before synchronous response
- webhook redelivery
- worker restart
- provider 429/5xx/timeout
- network disappears after request dispatch
- device restart with queued work
- migration from old local schema
- unauthorized cross-location request
- shift close races with a new transaction
- hardware unavailable after payment

The test should assert the **remaining business state**, not only the HTTP status.

---

## 23. Production evidence boundary

Architecture, code, and repository tests still do not prove:

- real payment readers
- physical printers/scanners/drawers
- production network behavior
- external-provider credentials and settlement
- representative load/throughput
- production deployment topology
- operator training
- field migration rollout
- monitoring and incident response

I treat those as explicit external acceptance gates.

That is part of reliability too: knowing which claims the available evidence does and does not support.

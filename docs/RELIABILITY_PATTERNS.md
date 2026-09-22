# Reliability Patterns

This document summarizes patterns I use when designing operational software where failures can affect payments, orders, employee workflows, customer experience, or business state.

The exact implementation depends on the system.

These are engineering patterns, not a claim that every project uses every pattern in exactly the same way.

## Public implementation evidence

The strongest public examples currently live in [dthompsonfl/pos](https://github.com/dthompsonfl/pos).

Useful references:

- [SYNC.md](https://github.com/dthompsonfl/pos/blob/main/SYNC.md) — offline persistence, sync outbox, idempotency keys
- [PAYMENTS.md](https://github.com/dthompsonfl/pos/blob/main/PAYMENTS.md) — payment boundaries and retry/idempotency requirements
- [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md) — scoped payment context, idempotency, and release-state verification
- [README.md](https://github.com/dthompsonfl/pos/blob/main/README.md) — current implementation status and known production gaps

## 1. Idempotent commands

Side-effecting operations should be safe when the same logical request is delivered more than once.

Examples:

- payment creation
- order submission
- refund initiation
- inventory adjustment
- shift close
- external integration commands

A useful model is:

```text
command
  |
  v
derive stable business operation key
  |
  v
look up prior execution
  |
  +--> completed ----> return recorded result
  |
  +--> in progress --> return/observe current state
  |
  +--> absent -------> execute and persist result
```

The operation key should represent the business action when possible, not merely a random HTTP request.

### Implementation concern

An idempotency record itself can race.

A production implementation may therefore need:

- a unique database constraint
- atomic insert/update behavior
- transaction boundaries
- result persistence
- expiration/retention rules
- scoping by tenant/location/register/provider

## 2. Explicit state machines

If a workflow has meaningful stages, I prefer to model them explicitly.

Instead of:

```text
status = arbitrary string
```

prefer:

```text
DRAFT -> SUBMITTED -> PROCESSING -> COMPLETED
                    \-> FAILED_RETRYABLE
                    \-> RECONCILIATION_REQUIRED
```

Benefits:

- invalid transitions can be rejected
- retries become easier to reason about
- the UI can represent uncertainty honestly
- audit history becomes meaningful
- recovery logic starts from a known state

## 3. Server-authoritative transitions

Clients request transitions; they should not independently declare business-critical state.

For example:

```text
client: request "mark order paid"

server:
  - validate identity
  - validate permission
  - validate tenant/location context
  - validate current state
  - validate payment evidence
  - perform transition
  - record audit context

client:
  render authoritative result
```

This reduces drift between:

- mobile
- web
- POS
- admin
- KDS
- background workers

## 4. Retry classification

Not every failure should retry.

### Retryable

Examples:

- temporary network failure
- rate limiting
- transient infrastructure failure
- provider timeout where idempotency makes retry safe

### Not retryable

Examples:

- validation failure
- permission denial
- invalid state transition
- malformed external payload
- unsupported operation

### Reconciliation required

Examples:

- an external provider may have completed the operation but the local system did not receive a definitive response
- webhook and synchronous result disagree
- local state and provider state diverge
- a timeout occurred after an irreversible external request

Blind retries are dangerous when an operation has financial or irreversible side effects.

## 5. Durable outbox pattern

When a database state change and an external event must stay logically consistent, a durable outbox can avoid this failure:

```text
database commit succeeds
event publish fails
downstream system never sees the change
```

A common pattern:

```text
transaction:
  update business state
  insert outbox event

worker:
  read unpublished event
  publish / dispatch
  record delivery state
```

Consumers still need idempotency because at-least-once delivery can produce duplicate events.

### Public evidence

The POS repository documents a local Room write and sync outbox being created together, then drained by background work.

## 6. Webhook reconciliation

Third-party webhooks are untrusted external input.

Important controls include:

- signature verification
- replay protection where supported
- event deduplication
- schema validation
- mapping to known local entities
- idempotent processing
- observable failures
- reconciliation against provider state when necessary

A webhook is evidence of an external event. It should not bypass local business invariants or authorization assumptions.

## 7. Optimistic UI with authoritative confirmation

Fast interfaces are useful, but optimistic behavior should not fabricate success for high-consequence operations.

### Good candidates for optimism

- navigation
- local selection
- reversible display state
- low-risk preference changes

### Require authoritative confirmation

- payment success
- refund completion
- irreversible fulfillment changes
- permission-sensitive overrides
- financial reconciliation

## 8. Offline-aware design

Offline behavior should be risk-based.

I separate actions into:

- **locally safe**
- **queueable with reconciliation**
- **must fail closed**

Example:

```text
cached menu read      -> locally safe
draft order editing   -> locally safe
telemetry/event queue -> often queueable
payment capture       -> authoritative/provider connectivity required
manager override      -> authoritative authorization required
```

### Public evidence

The POS repository uses Room-backed local persistence and documents an outbox-based synchronization design.

## 9. Fail closed for incomplete production integrations

A missing production integration should not silently fall back to fake success.

Examples:

- no real card reader bound -> card-present checkout unavailable
- no release signing credentials -> release build fails
- no hardware transport -> hardware action reports unavailable
- no authorization context -> protected command denied

### Public evidence

The POS repository explicitly documents simulated providers as development-only and disables unsupported release paths rather than treating them as real integrations.

## 10. Concurrency and race conditions

Correct single-request behavior does not guarantee correct concurrent behavior.

High-value questions include:

- can two terminals update the same order?
- can two workers consume the same job?
- can two payment requests use the same logical operation?
- can stale clients overwrite newer state?
- is stock decremented atomically?
- can a shift close while new transactions are still arriving?

Potential controls include:

- database constraints
- optimistic concurrency/version columns
- row-level locking where appropriate
- compare-and-set transitions
- idempotent consumers
- serialized processing for selected workflows

## 11. Auditability

For high-consequence operational changes, useful audit context can include:

- actor
- timestamp
- prior state
- requested transition
- resulting state
- reason / override reason
- correlation identifier
- source surface/device
- tenant/location/register context
- provider reference where appropriate

Audit history should be designed intentionally rather than reconstructed later from generic logs.

## 12. Observability by business workflow

Infrastructure metrics alone are not enough.

Useful questions include:

- how many orders are stuck in a nonterminal state?
- how many payments require reconciliation?
- how many retries occur per provider?
- which locations or devices are experiencing failures?
- how long do workflows remain in each state?
- are duplicate commands increasing?
- how often are manager overrides used?

This connects technical reliability to actual operational impact.

## 13. Timeouts and deadlines

Every external call should have a deliberate timeout strategy.

Questions include:

- how long can the user wait?
- can the operation continue asynchronously?
- is retry safe?
- can cancellation propagate?
- what state remains after timeout?
- does the caller know whether the external side effect happened?

A timeout is not automatically equivalent to a failed business operation.

## 14. Backpressure and overload

A system should degrade intentionally under load.

Depending on the workflow, controls may include:

- queue limits
- concurrency caps
- rate limiting
- admission control
- circuit breakers
- priority queues
- deferred noncritical work

Critical order/payment paths should not compete equally with low-value background work during overload.

## 15. Migration reliability

Schema changes affect runtime correctness.

Migration planning should account for:

- backward compatibility during deployment
- large-table changes
- defaults and nullability
- application version skew
- rollback strategy
- data backfill
- destructive changes
- mobile/offline schema versions

The POS public repository exposes Room schema versioning and migration handling as one example of this concern.

## 16. Security is part of reliability

An operation that succeeds for the wrong actor is not reliable.

For sensitive workflows, reliability includes:

- authentication
- authorization
- scoping
- input validation
- secret handling
- provider verification
- auditability

## 17. Recovery is part of the design

A production workflow is incomplete if the design describes only how it succeeds.

For every important workflow I try to answer:

1. What can fail?
2. What state remains after failure?
3. Can the operation be retried safely?
4. How does the system know whether an external side effect happened?
5. What does the operator see?
6. What can recover automatically?
7. When is human intervention required?
8. Is the final state auditable?
9. Can the system explain what happened after the fact?

That is the reliability standard I aim for in operational software.

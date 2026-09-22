# Reliability Patterns

This document summarizes patterns I use when designing operational software where failures can affect payments, orders, employee workflows, or business state.

The exact implementation depends on the system. These are architectural principles, not a claim that every project uses every pattern.

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

The key should represent the business operation when possible, not simply a random request identifier.

## 2. Explicit state machines

If a workflow has meaningful stages, I prefer to model those stages explicitly.

Instead of allowing:

```text
status = arbitrary string
```

prefer:

```text
DRAFT -> SUBMITTED -> PROCESSING -> COMPLETED
                    \-> FAILED_RETRYABLE
                    \-> RECONCILIATION_REQUIRED
```

Benefits include:

- invalid transitions can be rejected
- retries become easier to reason about
- the UI can represent uncertainty honestly
- audit history becomes meaningful
- recovery logic has a defined starting point

## 3. Server-authoritative transitions

Clients request transitions; they should not independently declare business-critical state.

For example:

```text
client: "attempt to mark order paid"
server:
  - validates actor
  - validates current state
  - validates payment evidence
  - performs transition
  - records result
client: renders authoritative result
```

This reduces drift between mobile, web, POS, admin, and background workers.

## 4. Retry classification

Not every failure should retry.

I generally think about failures in categories:

### Retryable

Examples:

- temporary network failure
- provider timeout with known-safe idempotency
- rate limiting
- transient infrastructure failure

### Not retryable

Examples:

- validation failure
- permission denial
- invalid state transition
- malformed external payload

### Reconciliation required

Examples:

- external provider may have completed the operation, but the local system did not receive a definitive response
- webhook and synchronous result disagree
- local state and provider state diverge

Blind retries are dangerous when an operation has financial or irreversible side effects.

## 5. Durable outbox pattern

When a database change and an external event must remain consistent, a durable outbox can help avoid the classic problem:

```text
DB commit succeeds
event publish fails
system now disagrees with downstream consumer
```

A common pattern:

```text
transaction:
  update business state
  insert outbox event

worker:
  read unpublished outbox event
  publish / dispatch
  mark delivered
```

Delivery still needs idempotent consumers because at-least-once delivery can produce duplicates.

## 6. Webhook reconciliation

Third-party webhooks are useful but should be treated as untrusted external input.

Important controls include:

- signature verification
- replay protection where supported
- event deduplication
- schema validation
- mapping to known local entities
- idempotent processing
- observable failures
- reconciliation against provider state when necessary

The webhook is evidence of an external event, not an excuse to bypass local authorization or business invariants.

## 7. Optimistic UI with authoritative confirmation

Fast interfaces are useful, but optimistic behavior should not fabricate success for high-consequence operations.

A safe distinction:

### Good candidates for optimistic UI

- local navigation
- temporary visual selection
- low-risk preference changes
- reversible presentation state

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

This is more useful than applying "offline-first" indiscriminately.

Example:

```text
cached menu read      -> locally safe
draft order editing   -> locally safe
telemetry/event queue -> often queueable
payment capture       -> authoritative connectivity required
manager override      -> authoritative authorization required
```

## 9. Fail closed for incomplete production integrations

A missing production integration should not silently fall back to a fake success path.

Examples:

- no real card reader bound -> card-present checkout unavailable
- no release signing credentials -> release build fails
- no hardware transport -> hardware action reports unavailable
- no authorization context -> protected command denied

The public [Enterprise POS Android](https://github.com/dthompsonfl/pos) repository documents this approach explicitly.

## 10. Auditability

For high-consequence operational changes, useful audit context can include:

- actor
- timestamp
- prior state
- requested transition
- resulting state
- reason / override reason
- correlation identifier
- source surface/device
- provider reference when appropriate

Audit logs should be designed intentionally rather than inferred later from generic application logs.

## 11. Observability by business workflow

Infrastructure metrics alone are not enough.

Useful business-level questions include:

- how many orders are stuck in a nonterminal state?
- how many payments need reconciliation?
- how many retries are occurring by integration?
- which locations or devices are experiencing failures?
- how long do workflows spend in each state?
- are duplicate commands increasing?

This helps connect technical reliability to actual operational impact.

## 12. Recovery is part of the design

A production workflow is incomplete if the design describes only how it succeeds.

For every important workflow I try to answer:

1. What can fail?
2. What state remains after failure?
3. Can the operation be retried safely?
4. How does the system know whether the external side effect happened?
5. What does the operator see?
6. What can the system recover automatically?
7. When is human intervention required?
8. Is the final state auditable?

That is the reliability standard I aim for in operational software.

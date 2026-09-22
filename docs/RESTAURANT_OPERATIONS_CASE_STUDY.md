# Restaurant Operations Platform — Sanitized Engineering Case Study

> This document describes architecture, implemented concerns, and engineering decisions from active restaurant-technology work. It intentionally omits proprietary source code, credentials, customer data, production URLs, and business-sensitive details.

## Evidence boundary

This case study combines two kinds of material:

1. **Implemented project concerns** from active private restaurant systems and public POS work.
2. **Engineering patterns** I use to reason about reliability, recovery, and automation.

Where public code demonstrates a concept directly, I link to it. Where production source is private, I describe the architecture without presenting private implementation details as public proof.

Related public evidence:

- [Enterprise POS Android](https://github.com/dthompsonfl/pos)
- [POS sync design](https://github.com/dthompsonfl/pos/blob/main/SYNC.md)
- [POS payment design](https://github.com/dthompsonfl/pos/blob/main/PAYMENTS.md)
- [POS production-readiness verification](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md)

## Problem

Restaurant software sits directly in the path of revenue and guest experience.

A single order may touch:

- customer ordering
- menu availability
- pricing and modifiers
- payment processing
- POS/register state
- kitchen routing
- fulfillment
- employee permissions
- operational devices
- reporting
- reconciliation

The system therefore has to do more than support a happy-path CRUD workflow.

It needs to answer questions such as:

- What is the authoritative order state?
- Was a payment actually captured?
- Can this operation be safely retried?
- Did the kitchen receive the order?
- What happens if connectivity disappears halfway through an action?
- Which user is allowed to override the state?
- How does an operator recover without creating duplicate financial effects?
- How does the system distinguish a temporary integration problem from a real business failure?

## Product principle

The operating principle is:

> **Restaurant employees should operate the restaurant — not manage the software.**

Software should resolve deterministic problems automatically where it can, surface exceptions clearly where it cannot, and avoid asking frontline employees to understand implementation details.

## High-level domain model

```text
Customer / Ordering
        |
        v
Order Orchestration
        |
        +------> Payments
        |
        +------> POS / Register State
        |
        +------> Kitchen / KDS
        |
        +------> Fulfillment
        |
        +------> Notifications / Exceptions
        |
        v
Operational Reporting / Reconciliation
```

Supporting domains include:

- menu/catalog
- pricing/modifiers
- employee identity
- RBAC
- locations/registers
- devices
- shifts
- cash/tenders
- configuration
- audit history

## 1. Authoritative workflow state

One of the most important architectural rules is that clients should not independently decide that a business workflow is complete.

For example, an order UI may request a transition, but the authoritative service should determine whether the transition is valid.

Conceptually:

```text
DRAFT
  -> SUBMITTED
  -> PAYMENT_PENDING
  -> PAID
  -> ACCEPTED
  -> IN_PREPARATION
  -> READY
  -> FULFILLED
```

Real systems also need explicit exceptional states rather than hiding ambiguity:

```text
PAYMENT_REQUIRES_RECONCILIATION
FULFILLMENT_BLOCKED
INTEGRATION_RETRY_PENDING
CANCEL_REQUESTED
REFUND_PENDING
```

The exact state model varies by implementation, but the principle remains:

> **Uncertainty should be represented explicitly instead of being converted into false success.**

## 2. Idempotency and duplicate execution

Restaurant operations are frequently retried accidentally or automatically.

Examples:

- an employee double-taps an action
- a mobile client retries after a timeout
- a background worker restarts
- an external provider sends a duplicate webhook
- a customer retries after an uncertain response

For side-effecting operations, a retry should not create a second financial or operational effect.

Typical design:

```text
request
  |
  +--> stable operation identity
  |
  +--> lookup prior execution
          |
          +--> completed -> return recorded result
          |
          +--> processing -> return/observe current state
          |
          +--> absent -> execute once
```

### Public evidence

The public POS repository documents:

- an offline sync outbox with idempotency keys
- payment retries requiring durable idempotency
- merchant/store/register-scoped payment idempotency

See:

- [SYNC.md](https://github.com/dthompsonfl/pos/blob/main/SYNC.md)
- [PAYMENTS.md](https://github.com/dthompsonfl/pos/blob/main/PAYMENTS.md)
- [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md)

## 3. Payment uncertainty

A dangerous payment failure mode is:

1. the provider receives a request,
2. connectivity fails,
3. the client does not receive the final result,
4. the user retries,
5. the system creates a duplicate charge.

A safer system treats uncertain provider state as something to reconcile.

```text
local request
    |
    v
provider operation
    |
    +--> confirmed --------> record authoritative result
    |
    +--> declined ---------> record failure
    |
    +--> unknown ----------> reconciliation required
```

"Unknown" is a valid technical state.

Pretending an uncertain external operation cleanly failed can be more dangerous than acknowledging that reconciliation is required.

### Public evidence

The POS project includes server-side Stripe integration scaffolding and explicit release safeguards. Card-present payment paths fail closed when a real Terminal SDK bridge is unavailable rather than silently substituting simulated success.

## 4. Kitchen workflows

Kitchen state is operational, not cosmetic.

A KDS needs enough context to answer:

- what was ordered
- when it entered the kitchen
- what station owns it
- whether it was acknowledged
- whether it changed after submission
- whether it is blocked
- whether it is ready
- whether customer-facing state reflects reality

The interface should minimize interaction cost.

A KDS is not a general-purpose administrative dashboard.

## 5. Offline-aware operation

Some restaurant workflows need to remain useful during transient connectivity failures.

Offline capability does **not** mean every operation should proceed independently.

I separate actions into three risk categories.

### Locally safe

Examples may include:

- reading cached menu data
- maintaining local UI state
- drafting an order
- viewing previously synchronized operational information

### Queueable with reconciliation

Examples may include:

- selected non-financial state updates
- telemetry
- local operational events
- deferred synchronization with deterministic conflict rules

### Must fail closed or require authoritative connectivity

Examples commonly include:

- payment capture
- authorization-sensitive overrides
- irreversible external-provider operations
- actions where duplicate execution has financial consequences

### Public evidence

The POS repository documents an offline-first local persistence model using Room plus a synchronization outbox processed in the background.

That implementation is useful precisely because local capability is separated from server-authoritative or provider-authoritative operations.

## 6. Permissions and manager overrides

Restaurant permissions often require more granularity than a single admin/non-admin split.

Typical identities include:

- employee
- kitchen
- shift lead
- manager
- owner
- device/service identity

Sensitive actions may require:

- explicit permission
- manager override
- reason capture
- audit entry
- server-side enforcement

UI visibility is not authorization.

## 7. Menu and configuration consistency

Menus, prices, modifiers, taxes, availability, and location settings can drift if each client maintains independent business rules.

The preferred architecture is:

```text
canonical configuration/domain services
        |
        +--> public ordering
        +--> POS
        +--> KDS
        +--> admin
        +--> mobile
        +--> reporting
```

This reduces disagreement between operator-facing and customer-facing systems.

## 8. Integration boundaries

External systems should be isolated behind explicit boundaries.

Examples include:

- payment processors
- POS providers
- delivery providers
- messaging services
- identity systems
- hardware integrations

A useful integration boundary should make it clear:

- what data leaves the system
- what identifier correlates the request
- whether the operation is retryable
- whether idempotency is available
- how external state is reconciled
- what happens when the provider is unavailable

This is especially important when an external provider has different consistency or failure semantics than the local application.

## 9. Observability

Operational systems need to distinguish among:

- customer input error
- operator error
- invalid application state
- authorization failure
- integration failure
- infrastructure failure
- retryable failure
- reconciliation-required state

Useful correlation context can include:

- order ID
- location/register context
- provider operation ID
- workflow state
- transition attempted
- retry count
- reconciliation status

The goal is not just logging. The goal is being able to answer:

> **What happened to this business operation, and what should happen next?**

## 10. AI and operational automation

AI can be useful in restaurant operations for tasks such as:

- classification
- exception triage
- support summarization
- recommendation
- workflow assistance
- anomaly explanation

But critical business constraints should remain deterministic.

The model should not be the sole authority deciding whether:

- money moved
- an order legally changed state
- a user has permission
- an irreversible action should execute

A pattern I prefer:

```text
AI interprets / proposes
        |
        v
deterministic policy validates
        |
        v
authorized tool/service executes
        |
        v
result is observed
        |
        v
reconcile / escalate if necessary
```

AI adds flexibility; application logic retains control of business invariants.

## 11. Operator experience

Reliability is partly a user-interface problem.

When something fails, the operator should not need to infer technical state from generic messages.

A useful exception flow answers:

1. What happened?
2. Is the business operation complete?
3. Is it safe to retry?
4. Will the system retry automatically?
5. Is manager action required?
6. Is customer communication required?

The operator interface should expose the minimum information required to make the correct decision.

## 12. Security and data boundaries

Operational convenience should not bypass security.

Important controls include:

- server-side authorization
- location/register scoping
- least-privilege roles
- secret isolation
- signed/validated provider callbacks
- secure device identity
- no raw production provider secrets in client applications
- auditability for sensitive overrides

The public POS project explicitly documents that provider secrets should remain server-side and should not be stored in Android client configuration.

## 13. What I optimize for

- predictable state
- safe retries
- explicit failure
- recoverability
- low operator cognitive load
- canonical business rules
- strong authorization boundaries
- observable workflows
- minimal unnecessary manual intervention
- honest production-readiness status

## 14. What remains environment-dependent

Architecture alone does not prove production behavior.

A restaurant system still requires real-world validation of:

- payment hardware
- printer/scanner/drawer hardware
- provider credentials
- network behavior
- concurrency under service load
- recovery after device/process restart
- webhook delivery
- kitchen-device ergonomics
- migration/upgrade behavior
- operational training

I treat those as explicit validation requirements rather than assuming source-level completeness equals production readiness.

## Related documents

- [Reliability Patterns](./RELIABILITY_PATTERNS.md)
- [Engineering Principles](./ENGINEERING_PRINCIPLES.md)
- [Selected Projects](./SELECTED_PROJECTS.md)
- [Technical Stack](./TECHNICAL_STACK.md)

# Restaurant Operations Platform — Sanitized Engineering Case Study

> This document describes architecture and engineering decisions from active restaurant-technology work. It intentionally omits proprietary source code, credentials, customer data, production URLs, and private business information.

## Problem

Restaurant software sits directly in the path of revenue and guest experience.

A single transaction may touch:

- customer ordering
- menu availability
- pricing and modifiers
- payment processing
- POS state
- kitchen routing
- fulfillment
- employee permissions
- devices
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

## Design goal

The operating principle is:

> **Restaurant employees should operate the restaurant — not manage the software.**

Software should resolve deterministic problems automatically where it can, surface exceptions clearly where it cannot, and avoid asking frontline employees to understand implementation details.

## High-level domains

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

For example, an order UI may request a transition, but the backend should determine whether that transition is valid.

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

The exact state model varies by implementation, but the principle remains: **uncertainty should be represented explicitly instead of converted into false success.**

## 2. Idempotency

Many restaurant operations can be repeated accidentally:

- an employee double-taps a button
- a mobile client retries after a timeout
- a job worker restarts
- an external provider sends a duplicate webhook
- a customer resubmits after an uncertain response

For side-effecting operations, retries must not create a second financial or operational effect.

Typical design:

```text
request
  |
  +--> operation key / business identity
  |
  +--> check prior result
          |
          +--> completed -> return recorded result
          |
          +--> processing -> return current state
          |
          +--> absent -> execute once
```

The important part is that idempotency is scoped to the business operation, not merely to a random HTTP request.

## 3. Payment uncertainty

A dangerous payment failure mode is:

1. the processor receives a request,
2. the local client loses connectivity,
3. the client does not receive the final response,
4. the user retries,
5. the system creates a second charge.

A safer system treats uncertain external state as something to reconcile.

```text
local request
    |
    v
payment intent / provider operation
    |
    +--> confirmed --------> record authoritative result
    |
    +--> declined ---------> record failure
    |
    +--> unknown ----------> reconciliation required
```

"Unknown" is a valid technical state. Pretending it is a clean failure can be more dangerous than acknowledging uncertainty.

## 4. Kitchen workflows

Kitchen state is operational, not cosmetic.

The system must preserve enough context to answer:

- what was ordered
- when it entered the kitchen
- what station owns it
- whether it was acknowledged
- whether it changed after submission
- whether it is blocked
- whether it is ready
- whether the customer-facing state reflects reality

Kitchen interfaces should minimize interaction cost. A KDS is not an administrative dashboard.

## 5. Offline-aware operation

Some restaurant functions need to remain useful during transient connectivity failures.

Offline capability does **not** mean allowing every operation to proceed independently.

Operations can be divided into categories:

### Safe locally

Examples may include:

- reading cached menu data
- maintaining local UI state
- drafting an order
- viewing previously synchronized operational information

### Queueable with explicit reconciliation

Examples may include:

- certain non-financial state updates
- telemetry
- local operational events
- deferred sync operations with deterministic conflict rules

### Must fail closed or require authoritative connectivity

Examples commonly include:

- payment capture
- authorization-sensitive overrides
- actions where duplicate execution has financial consequences
- irreversible external-provider operations

The decision should depend on business risk, not on a blanket "offline-first" label.

## 6. Permissions and manager overrides

Restaurant permissions often require more granularity than a single admin/non-admin split.

Examples:

- employee
- shift lead
- kitchen
- manager
- owner
- device/service identity

Sensitive actions may require:

- explicit permission
- manager override
- reason capture
- audit entry
- server-side enforcement

UI hiding is not authorization.

## 7. Menu and configuration consistency

Menus, pricing, modifiers, taxes, availability, and location-specific settings can easily drift if every surface maintains its own business logic.

The preferred approach is to keep configuration canonical and make clients consume validated domain state.

This reduces disagreement between:

- public ordering
- POS
- kitchen
- admin
- mobile
- reporting

## 8. Observability

Operational systems need to distinguish between:

- customer error
- operator error
- invalid state
- integration failure
- infrastructure failure
- data inconsistency
- recoverable retry
- reconciliation-required event

Logs should carry useful business correlation identifiers without exposing sensitive data unnecessarily.

Useful operational signals include:

- order ID
- location/register context
- provider operation ID
- workflow state
- transition attempted
- retry count
- reconciliation status

## 9. AI and automation

AI can be useful in restaurant operations for tasks such as:

- classification
- exception triage
- support summarization
- recommendation
- workflow assistance
- anomaly explanation

But critical business constraints should remain deterministic.

The model should not be the only authority deciding whether:

- money moved
- an order legally changed state
- a user has permission
- an irreversible action should execute

A useful model is:

```text
AI proposes / interprets
        |
        v
deterministic policy validates
        |
        v
tool / service executes
        |
        v
result is observed and reconciled
```

## What I optimize for

- predictable state
- safe retries
- explicit failure
- recoverability
- low operator cognitive load
- centralized business rules
- strong authorization boundaries
- observable workflows
- minimal manual intervention

## Related public work

The [Enterprise POS Android](https://github.com/dthompsonfl/pos) repository contains public examples of many adjacent concerns: payment abstractions, local persistence, release safeguards, backend boundaries, sync scaffolding, migrations, hardware abstractions, and explicit production-readiness documentation.

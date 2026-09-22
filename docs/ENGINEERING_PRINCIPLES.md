# Engineering Principles

These principles describe how I turn operational requirements into implementation decisions. They are not slogans or claims that every repository applies every principle identically. Where public code demonstrates a pattern, I link it; where the principle is broader than the public evidence, I label it as an engineering standard I apply.

## 1. Solve the operating problem, not the screen

A feature request is often phrased as a screen or button:

- “add a refund button”
- “let the kitchen recall an order”
- “make the register work offline”
- “let managers change a price”

The actual engineering problem is larger.

For a refund, I want to know:

- who may initiate it;
- which payment state permits it;
- whether the provider operation is idempotent;
- what happens after a timeout;
- how partial refunds are represented;
- how the local system learns the provider result;
- what appears in reconciliation and audit history.

I start from the business transition and its failure modes, then design the UI around that contract.

---

## 2. Make important state explicit

If a distinction changes behavior, recovery, authorization, or reporting, it should usually exist in the model.

I prefer:

~~~text
PROCESSING
SUCCEEDED
FAILED_RETRYABLE
FAILED_FINAL
RECONCILIATION_REQUIRED
~~~

over a single boolean such as:

~~~text
paymentSuccessful = true/false
~~~

The explicit model makes invalid transitions easier to reject and uncertain outcomes easier to represent honestly.

**Public example:** [KdsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-kds/src/main/java/com/enterprise/pos/feature/kds/state/KdsViewModel.kt) works with explicit order states rather than a generic kitchen-complete flag.

---

## 3. Give each business rule one canonical owner

Duplicated business rules become inconsistent business rules.

Examples that should have clear ownership:

- price calculation
- modifier validity
- authorization
- payment transition rules
- fulfillment transitions
- menu availability
- shift-close rules
- retry policy

Clients may project or preview canonical rules, but they should not become independent authorities.

For example:

~~~text
POS ----------+
online order -+--> canonical pricing service --> persisted order snapshot
admin --------+
~~~

This matters most when multiple clients evolve at different speeds.

---

## 4. Design commands to be idempotent

Networks retry. Users double-tap. Workers restart. Webhooks redeliver.

For consequential mutations, I want a stable operation identity and an atomic way to claim or observe it.

The implementation should answer:

- What is the logical business operation?
- Which scope makes that identity unique?
- Can two requests race?
- Where is the uniqueness enforced?
- Is the prior result durable?
- What happens if the process dies after an external side effect?

**Public example:** [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) scopes client-supplied payment idempotency keys by merchant/store/register.

**Important caveat:** when that public route has no caller-supplied key, it generates a new UUID. That is unique, but it is not a stable business-operation identity across a later independent retry. I treat that distinction as part of the design review, not something to hide.

---

## 5. Model uncertainty instead of converting it into failure

An external timeout does not prove the external system did nothing.

For payments, delivery providers, messaging, hardware, or other integrations, I distinguish:

- known success
- known failure
- retryable transport failure
- unknown external outcome
- reconciliation required

If the system cannot prove whether an irreversible action happened, it should preserve evidence and reconcile before repeating it.

This is especially important for money movement.

---

## 6. Fail closed when a production dependency is missing

Development simulators are useful. Silent production fallbacks are dangerous.

I prefer:

~~~text
real provider unavailable
        |
        v
explicit unavailable/configuration error
~~~

over:

~~~text
real provider unavailable
        |
        v
simulate success
~~~

**Public example:** [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) keeps simulated behavior separate and returns a configuration failure when real mode does not have the required Terminal bridge.

The same principle applies to:

- credentials
- signing keys
- authorization context
- production storage
- required device transports
- migration prerequisites

---

## 7. Treat recovery as part of the feature

A workflow is not complete when only its successful path is defined.

For important operations I ask:

1. What can fail?
2. What state survives?
3. Is retry safe?
4. Could an external side effect already have happened?
5. What can recover automatically?
6. What does the operator see?
7. When is human intervention required?
8. How is the final outcome audited?

This changes implementation choices early. It is why outboxes, reconciliation states, explicit transitions, and operator-facing exception queues matter.

---

## 8. Authorization belongs on the authoritative side

The UI can guide users by hiding unavailable actions. It cannot be the security boundary.

For protected mutations, the server should resolve and validate:

- authenticated identity
- role/permission
- tenant or organization
- location/store
- register/device when relevant
- target resource
- current state
- override/step-up requirements

The client should not be able to expand authority by changing an ID in a request.

Private restaurant source validates centralized authorization and RBAC work. Public [ECCB auth configuration](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts) and [permission constants](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/permission-constants.ts) provide separate inspectable examples of application authentication and typed permissions.

---

## 9. Scope is part of authorization

“User is authenticated” is not enough.

In multi-location or multi-role systems, correctness may depend on:

~~~text
identity
  + permission
  + tenant
  + location
  + register
  + resource
  + current workflow state
~~~

**Public example:** [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) requires merchant/store/register context and validates payment metadata against that context.

I still distinguish **context present in a request** from **context cryptographically or database-bound to the authenticated principal**. The latter is the stronger production guarantee.

---

## 10. Design each operational surface for its actual user

POS, KDS, admin, and owner dashboards should not be the same interface with different navigation.

### POS

Optimize for speed, large touch targets, obvious price/payment state, and immediate recovery.

### KDS

Optimize for glanceability, elapsed time, station relevance, and very fast state transitions.

### Admin

Optimize for safe configuration, auditability, permissions, and explainable impact.

### Owner / control plane

Optimize for exceptions, business outcomes, reconciliation, and decisions.

The domain model may be shared. The cognitive model should not be.

---

## 11. Keep automation deterministic at the point of authority

Automation is valuable when its boundaries are explicit.

A deterministic workflow can decide:

- whether a transition is legal;
- whether the actor is authorized;
- whether a retry is safe;
- which calculation is canonical;
- whether a threshold was crossed.

Those decisions should not depend on probabilistic text generation when the consequence is money, permissions, or irreversible state.

---

## 12. Put AI inside deterministic boundaries

My preferred pattern for AI-enabled operational software is:

~~~text
AI interprets / proposes
        |
        v
typed application boundary
        |
        v
deterministic policy validates
        |
        v
authorized service executes
        |
        v
system reads back actual result
        |
        v
audit / reconcile / escalate
~~~

AI may help understand intent, classify information, draft content, summarize evidence, or propose actions.

It should not independently decide:

- who is authorized;
- whether money moved;
- whether an order is legally in a state;
- whether a destructive transition is allowed;
- whether external success occurred.

Private source validates bounded OpenAI-compatible provider integration, including input/output limits, timeout handling, transient-only retries, and structured-response support. I keep that implementation private and expose only the architectural capability here.

---

## 13. Preserve data integrity before optimizing convenience

If a client-side shortcut can corrupt authoritative state, the shortcut is too expensive.

I prefer:

- database constraints for durable invariants;
- transactions around related writes;
- stable identifiers;
- explicit nullability;
- version/revision checks for stale writes;
- migrations that preserve deployment compatibility;
- canonical units and money representation.

The exact mechanism depends on the datastore, but the principle is consistent: invalid state should be difficult to represent.

---

## 14. Separate client convenience from business authority

Caching, optimistic UI, offline drafts, and local projections are valuable.

They should be treated as representations of authority, not replacements for it.

Examples:

- cached menu: useful for display; server still owns canonical price;
- local draft: useful offline; final submission still validates current rules;
- optimistic status: useful for low-risk UI; payment success waits for authoritative evidence;
- local role display: useful for navigation; server still enforces permission.

This keeps fast UX compatible with strong correctness.

---

## 15. Persist before replaying offline work

For an offline-capable mutation, the client should not rely on volatile memory.

A safer pattern is:

~~~text
user command
    |
    v
persist local command + operation identity
    |
    v
update permitted local projection
    |
    v
background sync
    |
    +--> success -> acknowledge
    +--> retryable -> backoff
    +--> conflict -> surface conflict
    +--> unknown -> reconcile
~~~

**Public example:** [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) and [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) implement Room-backed queued work and WorkManager processing.

---

## 16. Treat integration adapters as containment boundaries

Provider-specific data models and SDK behavior should terminate at an adapter.

I want domain code to depend on application-owned contracts such as:

~~~text
PaymentProvider
IdentityProvider
PrinterPort
DeliveryAdapter
CatalogSyncAdapter
~~~

not directly on provider SDK objects everywhere.

Benefits include:

- easier provider replacement;
- simpler testing;
- centralized error translation;
- secret isolation;
- clearer retry policy;
- less domain coupling.

**Public example:** [PaymentRouter.kt](https://github.com/dthompsonfl/pos/blob/main/payment-api/src/main/java/com/enterprise/pos/payment/router/PaymentRouter.kt) is a concrete payment-provider boundary.

---

## 17. Make observability answer business questions

CPU and memory graphs matter, but operators care about workflows.

I want to be able to answer:

- Which orders are stuck?
- Which payments have uncertain outcomes?
- Which provider is timing out?
- Which devices have growing sync backlogs?
- Which kitchen tickets are aging abnormally?
- Which locations are using repeated manager overrides?
- Which shifts do not reconcile?

Technical telemetry should make those questions easier to answer, not require reconstructing business state from raw logs.

---

## 18. Test failure modes, not only feature completion

A green happy-path test does not prove an operational workflow.

High-value scenarios include:

- duplicate command
- concurrent command
- stale client revision
- worker restart
- network loss during dispatch
- provider timeout after possible acceptance
- webhook redelivery
- unauthorized cross-location access
- migration from an older client schema
- hardware unavailable after a financial event
- process death between external success and local persistence

I want tests to assert what business state remains after the failure.

---

## 19. Make migrations part of the architecture

Schema changes are not an afterthought.

A migration plan should account for:

- deployment ordering
- application version skew
- data backfill
- nullability/default changes
- index creation
- destructive change avoidance
- rollback or roll-forward
- offline/mobile schema versions

**Public example:** [PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt) contains explicit Room migrations rather than relying on destructive recreation.

Private PostgreSQL systems add server-side migration validation and database-specific controls; those implementations remain private.

---

## 20. Prefer maintainable boundaries over clever abstractions

I prefer the simplest architecture that keeps ownership clear.

Good boundaries usually follow business or operational responsibility:

- order
- pricing
- payment
- identity/authorization
- kitchen
- fulfillment
- device
- reporting
- integration adapter

I avoid abstraction for its own sake. A new layer should earn its cost by reducing coupling, protecting an invariant, improving testability, or creating a real replacement boundary.

---

## 21. Be explicit about production readiness

I separate:

- source implemented;
- repository verified;
- simulated;
- externally unverified;
- deployment validated;
- field/hardware certified.

Code can be high quality without proving the environment around it.

Examples of evidence source alone cannot establish:

- a real payment reader survived disconnect/reconnect;
- a printer model works under production network conditions;
- a provider account settles correctly;
- a database migration was rehearsed against representative data;
- a system meets a throughput target;
- operators can recover during a real shift.

This is not conservative wording for its own sake. It prevents engineering decisions from being based on evidence that does not exist.

---

## 22. Own the end-to-end outcome

I do not treat backend work as complete when the endpoint returns the right JSON.

For an operational feature, ownership can include:

- domain model
- API/service contract
- persistence
- authorization
- frontend/mobile state
- integration behavior
- observability
- tests
- migration
- operator recovery
- deployment/release gate
- documentation

The boundary of responsibility should follow the business outcome closely enough that failures do not fall between teams or layers.

---

## Summary

The recurring standard behind these principles is **operational correctness**.

I want systems to:

- make important state explicit;
- keep business rules canonical;
- reject unauthorized actions at the authoritative boundary;
- survive duplicate delivery;
- distinguish failure from uncertainty;
- recover intentionally;
- degrade safely;
- make exceptions visible to operators;
- constrain AI behind deterministic policy;
- keep external integrations contained;
- preserve data integrity;
- state clearly where production evidence ends.

That is how I approach software expected to carry real business operations rather than only render a successful demo.

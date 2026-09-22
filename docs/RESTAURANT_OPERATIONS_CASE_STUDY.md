# Restaurant Operations Case Study

## Purpose

Restaurant software is a useful test of backend and product engineering because multiple operational surfaces are changing the same business state at the same time.

A customer can place an order while a cashier edits another order, a kitchen station is advancing tickets, a manager is changing menu availability, a payment processor is returning asynchronously, a tablet loses connectivity, and a shift is approaching close. The architecture has to preserve business truth across all of those interactions.

This document describes how I approach that problem.

It combines three kinds of material:

- **Public implementation evidence** from [dthompsonfl/pos](https://github.com/dthompsonfl/pos)
- **Sanitized private source-validated experience** from an active restaurant operating platform
- **Engineering patterns / target architecture** where the discussion goes beyond what the public repository alone proves

It does **not** claim that the public POS repository is fully production-certified. Its own [README](https://github.com/dthompsonfl/pos/blob/main/README.md) and [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md) document remaining provider, persistence, hardware, and environment-specific validation gaps.

---

## 1. The system I am reasoning about

A restaurant operating system is not one application.

~~~text
customer ordering
       |
       v
ordering / pricing authority
       |
       +--------------------+
       |                    |
       v                    v
POS / register          kitchen / KDS
       |                    |
       v                    v
payment state          prep / fulfillment
       |                    |
       +---------+----------+
                 |
                 v
        authoritative order state
                 |
        +--------+---------+
        |        |         |
        v        v         v
      admin   reporting  reconciliation
        |
        v
menu / roles / devices / configuration
~~~

Each surface has a different user, latency tolerance, failure mode, and permission model. They still have to converge on shared authoritative state.

---

## 2. Domain architecture

### Ordering

An order is not just a cart total. It is a lifecycle.

Typical concerns include:

- channel: counter, dine-in, takeout, delivery, online
- location
- register/station
- employee/operator
- customer
- service mode
- line items
- modifiers
- quantity
- pricing snapshot
- tax
- discounts
- fulfillment destination
- kitchen routing
- payment status
- order status
- revision/version
- timestamps and audit context

**Public evidence:** [FloorScreen.kt](https://github.com/dthompsonfl/pos/blob/main/feature-restaurant/src/main/java/com/enterprise/pos/feature/restaurant/screen/FloorScreen.kt) implements distinct restaurant order-start modes and table selection. [CheckoutViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-sales/src/main/java/com/enterprise/pos/feature/sales/state/CheckoutViewModel.kt) coordinates amount due and tender flow.

**Engineering consequence:** the authoritative system should derive financial and state transitions from validated order facts. A client should request an action; it should not be able to declare an arbitrary paid total or final state.

### POS and register state

A register is an operational actor, not simply a browser session.

Useful identity and scope can include:

- merchant/organization
- location/store
- register/station
- authenticated employee
- active shift
- device identity where appropriate
- current order
- operation/idempotency identity

**Public evidence:** [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) requires merchant, store, and register context for payment operations and scopes supplied idempotency keys using those values.

**Important limitation:** scope fields existing in a request are not, by themselves, proof that production authorization is complete. The service must bind those values to the authenticated principal and allowed resources. The private restaurant platform contains centralized RBAC and runtime authorization work; the public POS should not be used to claim more than its source proves.

### Menu and catalog

A restaurant menu is configuration with operational consequences.

The domain may include:

- menu
- section/category
- item/product
- variant
- modifier group
- modifier option
- required/optional selection rules
- minimum/maximum selections
- base price
- modifier price delta
- location availability
- time/day availability
- inventory or sold-out status
- kitchen routing key
- tax classification
- fulfillment eligibility

The rule I prefer is: **one canonical pricing/menu authority, many projections**.

POS, online ordering, kiosk, admin, and KDS should not each implement their own interpretation of pricing and modifiers.

### Modifiers and pricing

Modifier behavior is part of the order contract.

For example:

~~~text
item
  +-- size group: exactly 1
  +-- protein group: 0..1
  +-- toppings: 0..N
  +-- preparation: required choice
~~~

The server should validate:

- the modifier belongs to the item;
- the selection cardinality is valid;
- the option is enabled for the location/channel;
- the price adjustment is canonical;
- the resulting order snapshot is auditable.

A stale client may display old menu data. It should not be allowed to force stale pricing into authoritative state.

### Payment state

Payment state deserves its own state machine.

A useful conceptual model is:

~~~text
CREATED
  |
  v
PROCESSING
  |
  +--> SUCCEEDED
  |
  +--> FAILED
  |
  +--> CANCELED
  |
  +--> UNKNOWN / RECONCILIATION_REQUIRED
~~~

The last state is critical. A timeout does not prove that a provider did nothing.

**Public evidence:**

- [PaymentRouter.kt](https://github.com/dthompsonfl/pos/blob/main/payment-api/src/main/java/com/enterprise/pos/payment/router/PaymentRouter.kt) isolates provider selection from checkout logic.
- [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) implements Stripe PaymentIntent creation, capture, refund, lookup, context checking, supplied-key scoping, and signature verification for webhooks.
- [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) keeps simulated mode separate and fails closed when real mode lacks the required Terminal bridge.

**Limitations that matter:**

- The public repository does not prove a completed real Stripe Terminal SDK bridge.
- Public Square and Shopify payment implementations include simulated/scaffolded behavior.
- Real reader, settlement, disconnect, process-death, and field-network behavior requires external validation.
- The public webhook handler verifies signatures but does not establish a durable production reconciliation pipeline by itself.

### Kitchen / KDS

The KDS is not a generic order list. It is a projection optimized for food production.

Useful state includes:

- order/ticket
- station
- routed items
- fired/held state
- preparation status
- elapsed time
- urgency
- ready state
- recall/re-fire semantics
- completion/served state

**Public evidence:** [KdsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-kds/src/main/java/com/enterprise/pos/feature/kds/state/KdsViewModel.kt) reads open orders, filters kitchen-routed sent items, groups by station routing key, computes elapsed urgency, and applies ready/served/recall order-state actions.

A more mature kitchen system should avoid letting a UI gesture directly create ambiguous state. Commands such as fire, bump, recall, hold, and complete should have explicit semantics and stable operation identity where duplication is possible.

### Fulfillment

Fulfillment is the bridge between an order being commercially valid and the customer actually receiving it.

Different channels may need different state:

- dine-in: table / course / served
- takeout: accepted / preparing / ready / picked up
- delivery: accepted / preparing / ready / handed off / delivered
- third-party marketplace: external acknowledgement and reconciliation

This is an area where state explosion is preferable to false certainty. Collapsing every channel into a single generic “complete” flag makes recovery and support harder.

The private restaurant system contains fulfillment and provider-boundary work. This document intentionally keeps that implementation sanitized.

### Shifts and cash controls

Shifts connect employee identity, register responsibility, transaction activity, and closeout.

Useful concepts include:

- opened by
- register/location
- opening cash
- transactions during shift
- cash movement
- close request
- counted cash
- expected cash
- variance
- close reason/notes
- Z-report
- tip pooling/summary where applicable
- manager override or approval

**Public evidence:** [ShiftsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-shifts/src/main/java/com/enterprise/pos/feature/shifts/state/ShiftsViewModel.kt) implements shift open/close flow and invokes Z-report and tip-pool generation.

**Important limitation:** a UI flow invoking those services does not prove full financial reconciliation correctness. Repository-level reconciliation and field operations remain separate validation concerns.

### Employees and permissions

Restaurant authorization should be task-oriented.

Examples:

- cashier can create and tender permitted orders;
- kitchen staff can operate assigned kitchen workflows;
- manager can perform selected overrides;
- administrator can change configuration;
- owner has broader business authority;
- sensitive financial or security actions require stronger permission.

The rule I use is:

~~~text
UI permission = guidance
server permission = authority
~~~

The client can hide a button, but the authoritative service must still evaluate identity, role/permission, resource scope, and current state.

Private restaurant source validates centralized RBAC implementation and server-side authorization work. Public ECCB source separately provides inspectable examples of Better Auth and permission-management surfaces, but it is not a restaurant system and should not be conflated with restaurant deployment evidence.

### Devices and hardware

Operational devices create a second reliability boundary.

Potential devices include:

- POS tablets
- KDS displays
- receipt printers
- kitchen printers
- cash drawers
- scanners
- payment terminals
- bump bars
- network adapters

A useful abstraction separates:

~~~text
business command
      |
      v
application port/interface
      |
      v
device/provider adapter
      |
      v
physical transport
~~~

**Public evidence:** [EscPosPrinter.kt](https://github.com/dthompsonfl/pos/blob/main/hardware/src/main/java/com/enterprise/pos/hardware/escpos/EscPosPrinter.kt) contains USB, Bluetooth, network, and simulated printer transports.

The existence of transport code is not a claim that every physical device combination has been field-certified.

### Reporting and reconciliation

Reporting should be downstream from authoritative transactions, not a second source of business truth.

Useful outputs include:

- sales totals
- tender totals
- shift/Z reports
- voids/refunds
- discounts/overrides
- tax
- tips
- payment/provider discrepancies
- orders needing reconciliation
- device/integration exceptions

For financial reports, I prefer rebuilding from durable business facts where feasible rather than trusting mutable aggregates with unclear provenance.

---

## 3. Authoritative state

The most important architectural question is: **which component is allowed to decide the truth?**

A useful model is:

~~~text
client intent
    |
    v
authenticated command boundary
    |
    v
authorization + scope
    |
    v
validate current state / revision
    |
    v
apply canonical business rules
    |
    v
transaction / external operation
    |
    v
persist authoritative result
    |
    v
publish/project for POS, KDS, admin, reporting
~~~

This reduces the chance that each client invents its own version of the business.

### Client responsibilities

Clients are good at:

- collecting intent
- local interaction state
- rendering cached projections
- optimistic treatment of low-risk reversible actions
- explaining pending/error/conflict state

Clients should not be trusted to invent:

- authorization
- canonical price
- final payment success
- irreversible fulfillment state
- financial reconciliation
- global menu authority

---

## 4. Idempotency and duplicate commands

Operational systems naturally redeliver work.

Causes include:

- a cashier taps twice;
- a client times out and retries;
- a background worker restarts;
- a webhook is redelivered;
- a device reconnects and flushes queued work;
- two network paths race.

### Business-operation identity

For high-consequence commands I prefer an idempotency key tied to the logical operation.

~~~text
operation identity
    |
    +-- tenant/location scope
    +-- business object
    +-- command type
    +-- client-generated stable operation ID
~~~

A server implementation then needs an atomic guarantee:

~~~text
BEGIN
  insert operation record with UNIQUE(scope, operation_id)
  if duplicate:
      return/observe prior result
  perform or record transition
  persist result
COMMIT
~~~

### Public POS evidence and limitation

[PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) scopes a supplied idempotency key by merchant/store/register before passing it to Stripe operations.

However, if the caller omits a key, the current route generates a random UUID. That protects uniqueness but **does not make a later independent retry the same logical operation**. A production client therefore needs to provide and reuse a stable key for the business command.

Likewise, [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) gives each outbox event a unique key, and the retry engine reuses that event. This supports per-event deduplication; it should not be overstated as a complete cross-client business-operation idempotency design.

---

## 5. Idempotency races

The dangerous version of idempotency is:

~~~text
if key not found:
    perform side effect
    save key
~~~

Two concurrent requests can both pass the first check.

Correctness generally needs one of:

- unique database constraint plus transaction
- atomic compare-and-set
- serialized command handling
- provider-supported idempotency combined with local durable result tracking

### Public POS limitation

The current public backend [SyncEventStore.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/storage/SyncEventStore.kt) is explicitly an in-memory development store and documents the need for a production database unique constraint. It is useful architecture evidence, but it is not the production atomicity proof.

---

## 6. Retry taxonomy

“Retry on error” is too coarse.

### Safe/retryable

Examples:

- DNS/network interruption before request delivery is known
- 429/rate limit with retry-after handling
- transient 5xx
- queue worker process interruption when the command is idempotent

### Permanent / do not retry blindly

Examples:

- malformed input
- permission denial
- invalid state transition
- unsupported provider operation
- stale revision that requires refresh/conflict handling

### Unknown outcome / reconcile

Examples:

- request timed out after the provider may have accepted it
- local process died after capture but before local persistence
- webhook and synchronous result disagree
- external settlement exists with missing local finality

This category must exist explicitly. Treating “unknown” as “failed” invites duplicate money movement.

---

## 7. External provider uncertainty

Payments are the clearest example, but the same issue appears in:

- delivery marketplaces
- messaging providers
- identity providers
- tax services
- hardware controllers
- external POS/catalog providers

The application should preserve enough information to ask:

1. What did we intend?
2. What did we send?
3. What operation identity did we use?
4. What did the provider return?
5. Did we persist that result?
6. What does the provider say now?
7. Can we safely retry?
8. Does a person need to reconcile it?

A reconciliation state is a feature, not an admission of failure.

---

## 8. Outbox and asynchronous work

If a local transaction and a later external dispatch must remain logically connected, I prefer a durable outbox.

~~~text
database transaction
  - update business state
  - insert outbox row

worker
  - claim row
  - dispatch
  - record outcome
  - retry/reconcile according to policy
~~~

Consumers must still be idempotent because delivery may be at least once.

**Public evidence:** [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) and [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) implement a Room-backed local outbox and WorkManager retry/conflict handling.

**Boundary:** the device outbox is real source evidence. The public backend's durable production persistence remains incomplete.

---

## 9. Offline behavior

“Offline-first” should not mean “pretend every operation is safe offline.”

I classify commands by consequence.

| Class | Example | Expected behavior |
|---|---|---|
| **Locally safe** | browse cached menu, edit local draft | continue locally with visible freshness state |
| **Queueable** | selected nonfinancial mutation with stable operation identity | persist first, replay later, show pending/conflict state |
| **Requires authority** | payment capture, permission-sensitive override | fail closed or require authoritative connection |
| **Unknown outcome** | provider call timed out after dispatch | do not blindly repeat; reconcile |

Private restaurant/Android source includes explicit offline capability classification and durable command/outbox work. Public POS source demonstrates the local Room + WorkManager side of the model.

### Operator UX for offline state

Operators need language such as:

- Saved on this device; waiting to sync
- Menu may be stale
- Payment unavailable while offline
- Action sent; final outcome still being confirmed
- Conflict requires manager review

“Something went wrong” is not enough.

---

## 10. KDS reliability

Kitchen state should be durable and command-oriented.

Important cases include:

- duplicate fire
- stale POS resubmission
- station reroute
- hold/release
- bump/complete
- recall
- device restart
- network loss
- one station unavailable while others continue

The system should be able to distinguish:

~~~text
order exists
ticket projection exists
fire command accepted
station acknowledged
item completed
order ready
order served/fulfilled
~~~

Those are different facts.

The private restaurant system goes further than the public POS in this area; public source proves ticket projection and state actions but should not be interpreted as complete distributed-kitchen acknowledgement semantics.

---

## 11. Integration boundaries

I prefer provider-specific behavior to terminate at an adapter/service boundary.

### Payment processors

Domain code should not understand provider SDK objects.

~~~text
checkout service
    |
    v
payment port
    |
    +--> Stripe adapter
    +--> Square adapter
    +--> other provider adapter
~~~

Provider tokens and credentials should remain server-side except for intentionally publishable/session-scoped material.

### External POS / catalog systems

Synchronization should preserve:

- external identity
- local identity
- mapping/version
- source-of-truth decision
- conflict policy
- last observed state
- retry/reconciliation state

### Delivery systems

A delivery integration should not bypass the canonical order lifecycle merely because the external provider has a different status model. Translate at the boundary.

### Messaging

Notification failure should generally not roll back a successful order or payment. Messaging is a dependent workflow with its own retry/error policy.

### Hardware

Business rules should not import printer/scanner/terminal transport details. Hardware should be behind ports with explicit unavailable/error states.

### Identity providers

Authentication proves identity; application authorization still decides whether that identity may perform the restaurant action.

---

## 12. Security as correctness

For restaurant systems, security mistakes become operational mistakes.

### Server-side authorization

Every protected mutation should evaluate:

- authenticated identity
- permission/role
- tenant/merchant
- location
- register/device where relevant
- target resource
- current state
- step-up/override policy where required

### Location and register scope

A valid user at one location should not automatically be able to mutate another location merely by changing a client-supplied identifier.

Scope should be resolved and checked on the server.

### Manager overrides

An override is an auditable command, not a bypass.

Useful fields include:

- requesting operator
- authorizing manager
- permission used
- reason
- original value/state
- resulting value/state
- timestamp
- register/location
- related order/payment/shift

### Least privilege

Cashier, kitchen, manager, administrator, and owner workflows should receive the minimum authority needed for the task.

### Secret isolation

Provider credentials, signing secrets, service tokens, and database credentials should not be shipped to browser/mobile clients.

### Auditability

Sensitive state changes should be attributable after the fact. Security logs and business audit records serve different purposes and may require different retention.

---

## 13. Role-specific operational UX

The same information should not be presented identically to every user.

### POS

Optimize for:

- speed
- large touch targets
- minimal navigation
- clear price/payment state
- immediate recoverable errors
- visible offline/pending state
- manager escalation without abandoning the transaction

### KDS

Optimize for:

- glanceability
- station relevance
- elapsed time
- urgency
- large state-changing controls
- minimal typing
- clear recovery after reconnect/restart

### Admin

Optimize for:

- configuration clarity
- safe defaults
- impact previews
- permissions
- audit/history
- exception queues
- granular control without requiring technical vocabulary

### Owner dashboard

Optimize for:

- business outcome
- exception prioritization
- reconciliation
- trend/context
- actionability

An owner does not need a raw queue depth. They may need: “Three payments need reconciliation before close.”

---

## 14. Business-level observability

Infrastructure telemetry matters, but operational software also needs business signals.

Examples:

- orders stuck in nonterminal state
- payment attempts by provider/result
- reconciliation-required payments
- duplicate/idempotent replays
- KDS ticket age
- sync backlog by device/location
- failed or conflicted outbox entries
- manager overrides
- void/refund rate
- menu sync freshness
- device heartbeat/failure
- shift-close discrepancies

A useful operational event should answer **which business workflow is affected**, not only which process emitted a log line.

---

## 15. Timeouts, backpressure, and overload

### Timeouts

External calls need explicit deadlines.

After timeout, the system must know whether the correct next step is:

- retry
- poll/lookup
- reconcile
- cancel
- escalate

### Backpressure

If a provider or downstream service is slow, the system should not create unlimited work.

Possible controls include:

- queue caps
- worker concurrency limits
- rate limits
- circuit breakers
- priority classes
- admission control
- dropping/deferment of noncritical telemetry

During a restaurant rush, order/payment paths should not compete equally with low-value background work.

Public ECCB source provides a separate inspectable example of Redis/BullMQ queues and worker concurrency in [src/lib/jobs/queue.ts](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts). That is queue implementation evidence, not a claim that ECCB models restaurant workloads.

---

## 16. AI and automation

AI can be valuable in restaurant operations for interpretation, classification, support, suggested configuration, anomaly explanation, or workflow assistance.

I do not want a model directly deciding money movement, permissions, or irreversible state.

The boundary I prefer is:

~~~text
AI interprets or proposes
        |
        v
deterministic policy validates
        |
        v
authorized service/tool executes
        |
        v
system observes the actual result
        |
        v
reconcile or escalate if uncertain
~~~

Examples:

- AI may map an operator's natural-language request to a proposed menu change.
- Deterministic code validates that the item exists, the requested change is legal, the actor has permission, and the price is within policy.
- A typed service performs the mutation.
- The resulting state is read back and audited.

Private source validates bounded LLM provider integration work, including timeout/retry classification, structured response constraints, and provider abstraction. The portfolio does not expose that proprietary implementation.

---

## 17. Testing the failure paths

A restaurant system needs tests around what happens when dependencies misbehave.

High-value cases include:

- duplicate order submission
- duplicate payment command
- timeout before provider response
- provider success followed by local persistence failure
- webhook redelivery
- webhook before synchronous response
- stale order revision
- simultaneous terminal updates
- device offline during mutation
- restart with queued outbox work
- register/location mismatch
- unauthorized manager action
- shift close racing with a new transaction
- migration from an older offline database version
- printer disconnected after payment
- KDS unavailable while an order is accepted

The goal is not just “the request returned 200.” The goal is to prove the remaining business state is correct and recoverable.

---

## 18. Public proof map

The following public files are the most useful evidence for the patterns discussed above:

| Concern | Public evidence |
|---|---|
| Local durability / retry | [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) |
| Outbox schema | [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) |
| Mobile schema migration | [PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt) |
| Payment abstraction | [PaymentRouter.kt](https://github.com/dthompsonfl/pos/blob/main/payment-api/src/main/java/com/enterprise/pos/payment/router/PaymentRouter.kt) |
| Payment context/idempotency/webhook signature | [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) |
| Real vs simulated Stripe boundary | [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) |
| Checkout orchestration | [CheckoutViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-sales/src/main/java/com/enterprise/pos/feature/sales/state/CheckoutViewModel.kt) |
| Restaurant floor/order modes | [FloorScreen.kt](https://github.com/dthompsonfl/pos/blob/main/feature-restaurant/src/main/java/com/enterprise/pos/feature/restaurant/screen/FloorScreen.kt) |
| Kitchen projection | [KdsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-kds/src/main/java/com/enterprise/pos/feature/kds/state/KdsViewModel.kt) |
| Shift workflow | [ShiftsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-shifts/src/main/java/com/enterprise/pos/feature/shifts/state/ShiftsViewModel.kt) |
| Hardware transport boundary | [EscPosPrinter.kt](https://github.com/dthompsonfl/pos/blob/main/hardware/src/main/java/com/enterprise/pos/hardware/escpos/EscPosPrinter.kt) |
| Public status / known gaps | [README.md](https://github.com/dthompsonfl/pos/blob/main/README.md), [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md) |

---

## 19. Production reality

Source completeness is not production proof.

A restaurant system can look complete in code and still be unqualified until it has been tested with:

- real payment credentials
- real payment readers/terminals
- printer models and network transports
- scanners and cash drawers
- bump bars or kitchen input devices
- actual deployment networks
- network loss and recovery
- process death and restart
- duplicate/replayed provider events
- production secrets and rotation
- representative concurrency/load
- schema/data migration rollout
- monitoring/alerting
- backup/restore
- operator training
- shift-close/reconciliation procedures
- rollback

I treat those as separate acceptance gates rather than allowing architecture language to imply they happened.

---

## 20. What this case study demonstrates

The core engineering idea is that restaurant software should preserve **operational truth**.

That means:

- explicit state instead of ambiguous flags;
- one canonical owner for business rules;
- idempotent commands rather than hope that requests arrive once;
- retries classified by consequence;
- reconciliation for uncertain external outcomes;
- local/offline capability based on risk;
- server-side authorization and scope;
- provider/hardware boundaries;
- operator-visible recovery state;
- business-level observability;
- deterministic policy around AI;
- production claims that stop where the evidence stops.

That combination—backend correctness plus knowledge of how restaurant operations actually fail—is the part of this domain I am most interested in continuing to build.

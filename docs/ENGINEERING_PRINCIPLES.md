# Engineering Principles

These are the principles I use when designing, reviewing, and hardening software.

They are especially important in systems where software affects revenue, employees, customers, payments, devices, or operational continuity.

## 1. Solve the operational problem, not just the ticket

A requested feature is often a symptom.

Before implementing, I try to understand:

- who is doing the work
- what outcome they need
- what information they actually have
- what can go wrong
- what the software can determine automatically
- what truly requires human judgment
- what happens if nothing is changed

The goal is not to digitize unnecessary work. It is to remove it where possible.

## 2. Make state explicit

Hidden state creates ambiguity.

For important workflows, state should be:

- defined
- validated
- observable
- auditable where appropriate
- transitionable only through known rules

This is especially important for:

- payments
- orders
- approvals
- fulfillment
- shifts
- synchronization
- background jobs

## 3. Keep business rules canonical

If the same business rule is independently implemented in:

- web
- mobile
- POS
- KDS
- admin
- background jobs

then those implementations will eventually disagree.

I prefer business rules to live behind shared domain services, contracts, or server-authoritative APIs where practical.

## 4. Make retries safe

Networks fail. Users double-click. Workers restart. Providers resend events.

A robust system assumes important operations may be delivered more than once.

For side-effecting workflows, idempotency and reconciliation should be considered during design rather than added after duplicate effects occur.

## 5. Represent uncertainty honestly

"Unknown" can be a real system state.

If an external provider may have completed an operation but the application cannot prove the result, converting uncertainty into "failed" can create worse behavior than explicitly requiring reconciliation.

False certainty is dangerous in payment and fulfillment systems.

## 6. Fail closed when false success is dangerous

I would rather make an unavailable production capability obvious than silently substitute:

- simulated payments
- debug authentication
- fake hardware success
- destructive migration fallback
- insecure defaults
- unverified external state

Development conveniences should not accidentally become production behavior.

## 7. Design recovery with the happy path

For important workflows, implementation planning should include:

- retries
- timeouts
- duplicate events
- partial failure
- interrupted workflows
- stale clients
- provider disagreement
- reconciliation
- operator recovery

Recovery is part of the feature.

## 8. Authorization belongs at the authoritative boundary

Hiding a button is presentation logic, not security.

Protected operations should validate:

- identity
- tenant/location/register context
- role and permission
- current resource state
- business constraints

at the server or authoritative service boundary.

## 9. Optimize interfaces for the environment

A kitchen display, POS terminal, owner dashboard, and configuration portal have different interaction requirements.

The correct interface depends on:

- time pressure
- device
- user role
- error cost
- frequency of use
- information density
- training level
- environment

Operational software should not force every user into the same generic dashboard pattern.

## 10. Automate deterministic work

If the system already has enough trusted information to make a deterministic decision, repeatedly asking a human is usually waste.

Humans should be involved when:

- judgment is required
- confidence is insufficient
- policy requires approval
- an exceptional state needs context
- the consequences require deliberate confirmation

Not simply because automation was never implemented.

## 11. Use AI inside deterministic boundaries

I am interested in agentic systems, but I do not treat an LLM as the authority for critical invariants.

A pattern I prefer:

```text
model interprets / proposes
        |
        v
deterministic policy validates
        |
        v
authorized tool executes
        |
        v
system observes result
        |
        v
reconcile / escalate if necessary
```

AI can add flexibility while deterministic application logic retains control over:

- money
- permissions
- business state
- irreversible effects
- compliance-sensitive actions

## 12. Preserve data integrity over implementation convenience

Schema design, migrations, constraints, and transaction boundaries are product concerns.

I prefer:

- explicit constraints
- controlled migrations
- deterministic seed behavior
- safe defaults
- integrity checks
- clear ownership of authoritative fields
- version-aware rollout

## 13. Separate client convenience from system truth

A frontend can cache, optimize, and predict.

It should not become a competing source of truth for high-consequence business state.

When the client and authoritative service disagree, the system needs a defined reconciliation path.

## 14. Production readiness should be explicit

A repository should make it clear what is:

- implemented
- tested
- simulated
- scaffolded
- blocked
- production-ready
- dependent on hardware/provider validation
- still requiring operational verification

I do not consider ambiguity about readiness useful optimism.

The public [Enterprise POS Android](https://github.com/dthompsonfl/pos) repository intentionally documents these distinctions.

## 15. Test the failure modes that matter

Coverage percentage alone is not a reliability strategy.

High-value tests target things like:

- duplicate requests
- invalid transitions
- permission boundaries
- migration behavior
- stale state
- retry semantics
- calculation correctness
- data integrity
- provider failures
- security regressions
- serialization/concurrency where relevant

## 16. Prefer boring correctness over cleverness

Operational software benefits from code that another engineer can understand under pressure.

I value:

- explicit contracts
- small responsibilities
- clear naming
- predictable control flow
- strong typing
- maintainable domain boundaries

over clever abstractions that make behavior harder to trace.

## 17. Keep integration boundaries explicit

External services have their own:

- availability
- consistency models
- identifiers
- rate limits
- retry semantics
- security requirements

I prefer adapters/services that isolate those details rather than allowing provider-specific behavior to leak throughout the application.

## 18. Observability should explain business impact

A technically precise error is useful, but operational systems also need to explain what it means.

Good observability should help answer:

- what business operation was affected?
- what is its current state?
- was money moved?
- is a retry safe?
- does a user need to act?
- can the system recover automatically?

## 19. Security and reliability reinforce each other

A system that performs an operation for an unauthorized actor is not behaving reliably.

Security controls such as:

- authorization
- tenant isolation
- secret handling
- input validation
- audit history

are part of system correctness.

## 20. Own the outcome

I do not think a feature is finished merely because the code was merged.

Ownership extends through:

```text
requirements
  -> architecture
  -> implementation
  -> validation
  -> deployment readiness
  -> observation
  -> recovery
  -> refinement
```

The outcome in the real system is what matters.

## Related public evidence

- [Enterprise POS Android](https://github.com/dthompsonfl/pos)
- [Emerald Coast Community Band Platform](https://github.com/dthompsonfl/eccb.app)
- [Repair Portal](https://github.com/dthompsonfl/repair_portal)
- [Reliability Patterns](./RELIABILITY_PATTERNS.md)

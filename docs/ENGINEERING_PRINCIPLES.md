# Engineering Principles

These are the principles I use when designing, reviewing, and hardening software.

## 1. Solve the operational problem, not just the ticket

A requested feature is often a symptom.

Before implementing, I try to understand:

- who is doing the work
- what outcome they need
- what information they actually have
- what can go wrong
- what the software can determine automatically
- what truly requires human judgment

The goal is not to digitize unnecessary work. It is to remove it where possible.

## 2. Make state explicit

Hidden state creates ambiguity.

For important workflows, state should be:

- defined
- validated
- observable
- transitionable only through known rules

This is especially important for payments, orders, approvals, fulfillment, shifts, and synchronization.

## 3. Keep business rules canonical

If the same rule is independently implemented in:

- web
- mobile
- POS
- admin
- background jobs

then it will eventually disagree with itself.

I prefer business rules to live behind shared domain services, contracts, or server-authoritative APIs where practical.

## 4. Make retries safe

Networks fail. Users double-click. Workers restart. Providers resend events.

A robust system assumes important operations may be delivered more than once.

For side-effecting workflows, idempotency and reconciliation should be considered during design rather than added after duplicate effects occur.

## 5. Represent uncertainty honestly

"Unknown" can be a real system state.

If a provider may have completed an external operation but the application cannot prove the result, converting uncertainty into "failed" can create worse behavior than explicitly requiring reconciliation.

## 6. Fail closed when false success is dangerous

I would rather make an unavailable production capability obvious than silently substitute:

- simulated payments
- debug authentication
- fake hardware success
- destructive migration fallback
- insecure defaults

Development conveniences should not accidentally become production behavior.

## 7. Design recovery with the happy path

For important workflows, implementation planning should include:

- retries
- timeouts
- duplicate events
- partial failure
- interrupted workflows
- stale clients
- reconciliation
- operator recovery

Recovery is part of the feature.

## 8. Authorization belongs on the server

Hiding a button is presentation logic, not security.

Protected operations should validate identity, tenant/location context, role/permission, and relevant business constraints at the authoritative boundary.

## 9. Optimize interfaces for the environment

A kitchen display, POS terminal, owner dashboard, and configuration portal have different interaction requirements.

The correct interface depends on:

- time pressure
- device
- user role
- error cost
- frequency
- information density
- training level

Operational software should not force every user into the same generic dashboard pattern.

## 10. Automate deterministic work

If the system already has enough trusted information to make a deterministic decision, repeatedly asking a human is usually waste.

Humans should be involved when:

- judgment is required
- confidence is insufficient
- policy requires approval
- an exceptional state needs context

Not because the implementation never automated an obvious rule.

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

AI adds flexibility; application logic retains control over money, permissions, state transitions, and irreversible effects.

## 12. Preserve data integrity over implementation convenience

Schema design, migrations, constraints, and transaction boundaries are product concerns.

I prefer:

- explicit constraints
- reversible/controlled migrations
- deterministic seed behavior
- safe defaults
- integrity checks
- clear ownership of authoritative fields

## 13. Production readiness should be explicit

A repository should make it clear what is:

- implemented
- tested
- simulated
- scaffolded
- blocked
- production-ready
- still requiring external validation

I do not consider ambiguity about readiness a useful form of optimism.

## 14. Test the failure modes that matter

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

## 15. Prefer boring correctness over cleverness

Operational software benefits from code that another engineer can understand under pressure.

I value:

- explicit contracts
- small responsibilities
- clear naming
- predictable control flow
- strong typing
- maintainable domain boundaries

over clever abstractions that make behavior harder to trace.

## 16. Own the outcome

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

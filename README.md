# Dylan Thompson

**Backend & Platform Engineer building reliable software for real-world operations.**

I began developing software in **2021** through Shopify themes, apps, extensions, integrations, and commerce workflows. Since then, my work has expanded into backend engineering, SaaS platforms, restaurant operating systems, mobile applications, payments, workflow automation, and AI-enabled products.

My background also includes more than a decade of hands-on business operations. That experience shapes how I build software: I care about what happens when an integration times out, an employee repeats an action, a payment reaches an uncertain state, connectivity drops, or a busy operator does something the original requirements did not anticipate.

## Fast path for engineering reviewers

If you are reviewing my work for a backend, platform, or restaurant-technology role, start here:

1. [Restaurant Operations Case Study](docs/RESTAURANT_OPERATIONS_CASE_STUDY.md) — sanitized architecture and failure-mode thinking for ordering, POS, kitchen, payments, permissions, and operational automation.
2. [Reliability Patterns](docs/RELIABILITY_PATTERNS.md) — idempotency, explicit workflow state, retries, reconciliation, outbox patterns, offline behavior, and observability.
3. [Enterprise POS Android](https://github.com/dthompsonfl/pos) — public implementation evidence for offline persistence, sync outbox behavior, payment boundaries, scoped idempotency, migrations, and fail-closed release behavior.
4. [Technical Stack](docs/TECHNICAL_STACK.md) — where I have strong hands-on depth, adjacent experience, and areas I do not overstate.

## What I build

- Backend services and APIs
- Restaurant POS, KDS, ordering, and operations systems
- SaaS platforms and administrative control planes
- Workflow orchestration and operational automation
- Payment and commerce integrations
- Role-based and multi-tenant systems
- Offline-aware and recovery-oriented applications
- AI-assisted workflows and agentic systems
- Mobile and responsive operational applications

## Core stack

**Backend:** TypeScript · Node.js · PostgreSQL · Prisma · REST APIs  
**Web:** React · Next.js · TypeScript  
**Mobile:** Kotlin · Jetpack Compose · React Native architecture  
**Data & platform:** PostgreSQL · PostGIS · Redis · RBAC · authentication · CI/CD  
**Commerce:** Shopify · Stripe · Square · POS · payments  
**AI & automation:** LLM integrations · agent workflows · workflow orchestration

## Engineering focus

### Reliability

I design operational software around authoritative state, explicit transitions, idempotency, validation, recovery paths, and observable failures.

### Real-world operations

I build for environments where software affects customers, employees, revenue, payments, devices, and daily business operations.

### Canonical systems

I prefer centralized business rules, shared contracts, domain services, and explicit boundaries over duplicated logic spread across applications.

### End-to-end ownership

I work across requirements, architecture, data modeling, backend implementation, interfaces, integrations, security, testing, debugging, and production hardening.

## Selected public work

### [Enterprise POS Android](https://github.com/dthompsonfl/pos)

A multi-module Android/Kotlin POS prototype for restaurant and retail workflows with Compose UI, Room persistence, payment-provider abstractions, hardware abstractions, a Ktor backend module, CI wiring, and explicit production-readiness controls.

Public evidence includes:

- [offline sync/outbox design](https://github.com/dthompsonfl/pos/blob/main/SYNC.md)
- [payment architecture and idempotency requirements](https://github.com/dthompsonfl/pos/blob/main/PAYMENTS.md)
- [production-readiness verification](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md)

The repository intentionally documents incomplete production paths rather than presenting scaffolding or simulated integrations as finished.

### [Emerald Coast Community Band Platform](https://github.com/dthompsonfl/eccb.app)

A full-stack platform with public, authenticated member, and administrative surfaces using Next.js, React, TypeScript, Prisma, Better Auth, Redis/BullMQ, RBAC, CMS workflows, and background jobs.

### [Repair Portal](https://github.com/dthompsonfl/repair_portal)

Domain-heavy operational software covering intake, inspection, service planning, repair execution, materials, quality assurance, delivery, retention controls, and configurable business workflows.

### [Shopify Sync](https://github.com/dthompsonfl/shopifysync1)

Early commerce-integration work connecting Shopify and ERP workflows. Shopify development was the starting point of my software-engineering path in 2021.

## Restaurant operations engineering

A major focus of my current work is restaurant technology: ordering, POS, kitchen workflows, menu and pricing configuration, payments, employee permissions, devices, shift operations, and recovery-oriented backend design.

My design principle is simple:

> **Restaurant employees should operate the restaurant — not manage the software.**

Production source for active commercial systems remains private. This repository documents sanitized architecture, engineering decisions, reliability patterns, and public implementation evidence without exposing customer data, credentials, production configuration, or proprietary source.

## Portfolio documentation

- [Documentation Index](docs/README.md)
- [Career & Engineering Timeline](docs/CAREER_TIMELINE.md)
- [Selected Projects](docs/SELECTED_PROJECTS.md)
- [Restaurant Operations Case Study](docs/RESTAURANT_OPERATIONS_CASE_STUDY.md)
- [Reliability Patterns](docs/RELIABILITY_PATTERNS.md)
- [Engineering Principles](docs/ENGINEERING_PRINCIPLES.md)
- [Technical Stack](docs/TECHNICAL_STACK.md)

## Current interests

I am particularly interested in engineering problems involving:

- autonomous operational systems
- restaurant technology
- workflow orchestration
- reliable distributed business processes
- AI agents with deterministic guardrails
- payments and transaction systems
- small-business software
- developer tooling

## Principles I build around

```text
Make state explicit.
Make retries safe.
Make failures recoverable.
Keep business rules canonical.
Automate what the system can know.
Ask humans only when humans are actually needed.
```

---

**Open to backend, platform, and product-engineering opportunities involving complex operational systems.**

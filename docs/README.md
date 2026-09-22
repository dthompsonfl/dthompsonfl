# Portfolio Documentation

This directory is the technical layer behind the profile README. It is written for recruiters, hiring managers, and engineers who want to verify not only what I have worked on, but how I reason about operational software.

## Recommended review path

For a fast technical review:

1. Start with [Selected Projects](./SELECTED_PROJECTS.md) for source-backed project scope and implementation status.
2. Read [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md) for the deepest domain-specific backend discussion.
3. Read [Reliability Patterns](./RELIABILITY_PATTERNS.md) for failure handling, idempotency, state, retries, outbox, reconciliation, and recovery.
4. Use [Technical Stack](./TECHNICAL_STACK.md) to see where I claim primary depth, working experience, or only adjacent familiarity.
5. Use [Career Timeline](./CAREER_TIMELINE.md) to verify the boundary between operations experience and software-engineering tenure.

For a recruiter or hiring manager, the profile [README](../README.md), [Career Timeline](./CAREER_TIMELINE.md), and [Selected Projects](./SELECTED_PROJECTS.md) are the quickest path.

## Evidence model

Every material claim in this portfolio is intended to fit one of four categories.

### 1. Public implementation evidence

The implementation can be inspected directly in a public repository. When a specific claim matters, I prefer linking to the implementation file rather than relying only on project-level prose.

Primary public repositories:

- [dthompsonfl/pos](https://github.com/dthompsonfl/pos)
- [dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app)
- [dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal)
- [dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1)

### 2. Private source-validated experience

Some current systems remain private because they contain business-specific workflows, operational configuration, or other production-sensitive material. Those repositories may be used to validate my experience, but this public profile only describes them at a sanitized capability level.

Private source validation is not used as permission to disclose private code, customer or employee data, credentials, private URLs, financial records, internal identifiers, or security-sensitive implementation details.

### 3. Engineering pattern or principle

Some material describes a design standard I use rather than claiming that one repository implements every part of it.

Examples include:

- business-operation idempotency
- explicit state machines
- reconciliation after uncertain external outcomes
- deterministic policy around AI/automation
- backpressure and overload controls
- production migration and rollback design

Those sections are labeled as patterns, principles, or target architecture when they are not directly established by public source.

### 4. Adjacent knowledge

A technology can be relevant without being a core area of demonstrated depth. The clearest example in this portfolio is MongoDB: I understand document-database concepts, but I do not present MongoDB as equivalent to my PostgreSQL experience.

## Public evidence index

### Restaurant / POS

[dthompsonfl/pos](https://github.com/dthompsonfl/pos) is the strongest public repository for restaurant/POS engineering.

Direct implementation references:

- [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) — WorkManager-driven outbox draining, retry/backoff, conflict states
- [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) — Room-backed outbox schema and per-event idempotency key
- [PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt) — explicit Room migrations and unique outbox idempotency index
- [PaymentRouter.kt](https://github.com/dthompsonfl/pos/blob/main/payment-api/src/main/java/com/enterprise/pos/payment/router/PaymentRouter.kt) — provider abstraction and routing boundary
- [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) — authenticated Stripe routes, merchant/store/register context, scoped idempotency, webhook signature verification
- [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) — simulated-versus-real boundary and fail-closed real Terminal bridge requirement
- [CheckoutViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-sales/src/main/java/com/enterprise/pos/feature/sales/state/CheckoutViewModel.kt) — checkout orchestration and payment persistence
- [FloorScreen.kt](https://github.com/dthompsonfl/pos/blob/main/feature-restaurant/src/main/java/com/enterprise/pos/feature/restaurant/screen/FloorScreen.kt) — dine-in, takeout, retail, floor/table workflow
- [KdsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-kds/src/main/java/com/enterprise/pos/feature/kds/state/KdsViewModel.kt) — kitchen ticket projection and order-state actions
- [ShiftsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-shifts/src/main/java/com/enterprise/pos/feature/shifts/state/ShiftsViewModel.kt) — shift open/close, Z-report and tip-pool workflow
- [EscPosPrinter.kt](https://github.com/dthompsonfl/pos/blob/main/hardware/src/main/java/com/enterprise/pos/hardware/escpos/EscPosPrinter.kt) — USB, Bluetooth, network, and simulated printer transports

The repository is explicitly **not represented here as production-certified**. Its [README](https://github.com/dthompsonfl/pos/blob/main/README.md) and [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md) document remaining provider, persistence, hardware, and environment-dependent validation gaps.

### Full-stack web / auth / jobs

[dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app) provides public source evidence for:

- Next.js, React, and TypeScript
- Prisma with a MySQL/MariaDB datasource
- Better Auth integration
- role/permission management surfaces
- Redis/BullMQ queues and workers
- public, member, and admin application surfaces

Direct references:

- [package.json](https://github.com/dthompsonfl/eccb.app/blob/main/package.json)
- [Better Auth configuration](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts)
- [permission constants](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/permission-constants.ts)
- [BullMQ/Redis queue implementation](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts)

ECCB is evidence for MySQL/MariaDB, not PostgreSQL. PostgreSQL claims elsewhere in this portfolio are based on separately inspected current private systems.

### Python / Frappe / operational workflow

[dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal) provides public evidence for Python, Frappe/ERPNext, workflow-oriented domain modeling, role permissions, scheduler automation, and retention logic.

Direct references:

- [application hooks and scheduler registration](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/hooks.py)
- [retention/anonymization implementation](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/utils/compliance.py)
- [repair-order permission logic](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/permissions/repair_order.py)

### Historical Shopify evidence

[dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1) is intentionally treated as a thin historical artifact. Its public repository currently provides only minimal evidence of the Shopify-to-ERP synchronization direction. It supports the chronology of my 2021 starting point; it is not used to prove the depth of my current backend work.

## Documents

| Document | Purpose |
|---|---|
| [Career Timeline](./CAREER_TIMELINE.md) | Separates pre-software operating experience from the software-development path beginning in 2021 |
| [Selected Projects](./SELECTED_PROJECTS.md) | Project-by-project status, purpose, stack, responsibilities, source evidence, limitations, and engineering relevance |
| [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md) | Deep architecture discussion for ordering, POS, KDS, payments, menus, devices, shifts, fulfillment, reliability, security, and operational UX |
| [Reliability Patterns](./RELIABILITY_PATTERNS.md) | Concrete backend patterns for idempotency, retries, concurrency, outbox, webhooks, overload, migrations, auditability, and recovery |
| [Engineering Principles](./ENGINEERING_PRINCIPLES.md) | The implementation consequences of how I reason about software |
| [Technical Stack](./TECHNICAL_STACK.md) | Technology inventory grouped by demonstrated depth and evidence type |

## Production-readiness standard

I do not use architecture or source completeness as a substitute for production proof.

Depending on the system, production readiness may still require evidence from:

- real payment-provider credentials and settlement behavior
- actual card readers and payment terminals
- printers, scanners, cash drawers, bump bars, or other peripherals
- production networks and loss/recovery scenarios
- real deployment topology and secrets infrastructure
- load and concurrency testing at representative scale
- migration rehearsal and rollback
- monitoring and incident response
- operator training and field validation

Where those checks are not established by the available evidence, the portfolio says so.

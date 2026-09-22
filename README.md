# Dylan Thompson

**Backend & Platform Engineer**

I build backend and product systems for operational businesses: APIs, data models, authorization, payments, workflow automation, admin/control planes, mobile clients, and failure-recovery paths. A major area of my work is restaurant technology, where ordering, POS, kitchen, menu, payment, device, employee, and reconciliation workflows all have to stay correct under real operating pressure.

My software-development timeline begins in **2021**, when I started with Shopify themes, apps, extensions, storefront customization, commerce integrations, catalog workflows, and ERP synchronization. My career before and alongside software includes **more than a decade of hands-on business operations**. I do not count that operating background as software-engineering tenure; I use it as domain knowledge when designing systems for employees and customers who need the software to work during the busiest and least forgiving parts of the day.

## Current engineering focus

- **Backend & platform:** TypeScript, Node.js, REST/API architecture, PostgreSQL, Prisma, authentication, authorization, RBAC, jobs, integrations, migrations, observability, and release controls
- **Product engineering:** Next.js, React, admin/control-plane systems, role-specific operational UX, and end-to-end workflow ownership
- **Restaurant technology:** ordering, POS/register state, menus, modifiers and pricing, payments, KDS/kitchen workflows, shifts, devices, offline/recovery behavior, and reconciliation
- **Mobile:** Kotlin, Jetpack Compose, Room, WorkManager; current private React Native/Expo work is secondary to my Android/Kotlin depth
- **Automation & AI:** deterministic workflow automation, bounded LLM integrations, and agent-oriented systems where model output remains behind application-owned policy and authorization

## Public proof

| Project | What it demonstrates | Current evidence status |
|---|---|---|
| [dthompsonfl/pos](https://github.com/dthompsonfl/pos) | Kotlin/Compose POS, Room persistence, migrations, WorkManager sync, payment boundaries, KDS, restaurant floor flows, shifts, and hardware abstractions | **Active prototype / production hardening.** Strong source-level evidence; not represented as production-certified |
| [dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app) | Next.js/React/TypeScript application, Prisma on MySQL/MariaDB, Better Auth, RBAC surfaces, Redis/BullMQ workers, public/member/admin application boundaries | **Active application codebase.** Source evidence is public; deployment claims are intentionally separate |
| [dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal) | Python, Frappe/ERPNext, operational domain modeling, role permissions, scheduled workflow logic, configurable data retention | **Public operational application codebase** |
| [dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1) | Historical Shopify-to-ERP direction from the start of my development path | **Early artifact only.** It corroborates the origin of the work; it is not evidence of my current engineering depth |

For the POS repository, useful implementation starting points are the [Room/WorkManager sync engine](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt), [explicit Room migrations](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt), [payment router](https://github.com/dthompsonfl/pos/blob/main/payment-api/src/main/java/com/enterprise/pos/payment/router/PaymentRouter.kt), [scoped Stripe backend routes](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt), and [current verification notes](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md).

## Why operations matters to my engineering

Operational software is not finished when the happy path renders.

My operating background includes employee workflows, staffing and scheduling, cash controls, financial reconciliation, customer operations, SOPs, exception handling, and processes for nontechnical employees. That changes the questions I ask while engineering:

- What is the authoritative state?
- What happens when a request times out after the external side effect may already have occurred?
- Can the same command arrive twice without charging, firing, or applying it twice?
- What can continue offline, what may be queued, and what must fail closed?
- Can a manager understand and recover from an exception without engineering support?
- Are permissions and location/register scope enforced by the authoritative service rather than trusted from the client?
- Can the system explain what happened after a failure?

The deeper treatment is in [Restaurant Operations Case Study](./docs/RESTAURANT_OPERATIONS_CASE_STUDY.md) and [Reliability Patterns](./docs/RELIABILITY_PATTERNS.md).

## Portfolio map

- **[Documentation index](./docs/README.md)** — how to read the portfolio and how evidence is classified
- **[Career timeline](./docs/CAREER_TIMELINE.md)** — operations background → 2021 Shopify → integrations → full-stack → backend/platform → restaurant/mobile/AI systems
- **[Selected projects](./docs/SELECTED_PROJECTS.md)** — project-by-project scope, evidence, status, and limitations
- **[Restaurant operations case study](./docs/RESTAURANT_OPERATIONS_CASE_STUDY.md)** — ordering, POS, KDS, payments, offline/recovery, integrations, security, and operational UX
- **[Reliability patterns](./docs/RELIABILITY_PATTERNS.md)** — idempotency, state machines, retries, outbox, webhooks, concurrency, reconciliation, overload, migrations, and recovery
- **[Engineering principles](./docs/ENGINEERING_PRINCIPLES.md)** — how I make implementation decisions
- **[Technical stack](./docs/TECHNICAL_STACK.md)** — technologies grouped by actual depth rather than a keyword inventory

## Evidence and disclosure

Some of my most current systems are private because they contain business-specific workflows and production-sensitive implementation details. I use those repositories to validate experience, but this public profile only presents sanitized capability descriptions. I do not publish customer data, credentials, private URLs, proprietary source, internal identifiers, production configuration, or security-sensitive details.

Throughout this portfolio I distinguish between:

1. **public implementation evidence** that can be inspected directly;
2. **private source-validated experience** summarized without exposing proprietary material;
3. **engineering patterns or principles** that describe how I design systems; and
4. **adjacent knowledge** that I do not present as equivalent to hands-on depth.

That distinction is intentional. Source completeness, architecture quality, and production certification are not the same claim.

## Opportunities

I am open to backend, platform, product-engineering, and restaurant-technology roles where the work requires ownership across product behavior, data integrity, integrations, reliability, and real operational outcomes.

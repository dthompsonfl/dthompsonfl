# Technical Stack

This is a depth-based inventory, not a list of every technology I have encountered.

I use three levels:

- **Primary** — technologies and engineering areas I use deeply enough to design, implement, debug, and review substantial systems.
- **Working** — real hands-on implementation experience, but either narrower in scope or secondary to a stronger primary area.
- **Adjacent** — concepts or technologies I can work with and reason about, but I do not present them as equivalent to my strongest experience.

Evidence is also labeled by source type where that distinction matters:

- **Public** — inspectable in a public repository.
- **Private source-validated** — verified in current private repositories and summarized without exposing proprietary material.
- **Historical** — relevant to my progression but not representative of current depth.

---

## Backend and application languages

### TypeScript — Primary

My current backend and product engineering is heavily TypeScript-based.

Used for:

- Node.js services
- API routes and service layers
- domain/application logic
- authentication and authorization
- admin/control-plane systems
- workflow/job orchestration
- validation and typed contracts
- integration adapters
- tests and verification tooling

**Evidence:** public [ECCB package/runtime](https://github.com/dthompsonfl/eccb.app/blob/main/package.json); private source-validated current SaaS, control-plane, restaurant, finance, and domain systems.

### Node.js — Primary

Used as the runtime for backend services, API layers, workers, automation, validation tooling, and application servers.

My focus is less on framework branding and more on:

- authoritative service boundaries
- input validation
- failure classification
- idempotency
- authorization
- integration isolation
- observability
- background work
- migrations and release controls

**Evidence:** public ECCB; multiple current private TypeScript systems.

### JavaScript — Working

Used throughout earlier commerce work and current web/application ecosystems, including browser logic, build/release tooling, and framework integration.

### Python — Working

Used for operational business applications and automation.

**Public evidence:** [dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal), including [scheduler/application hooks](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/hooks.py) and [retention logic](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/utils/compliance.py).

---

## Web and product engineering

### Next.js — Primary

Used for full-stack product applications, public sites, authenticated application surfaces, admin/control planes, route/API boundaries, and server-rendered React systems.

**Public evidence:** [dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app).

**Private source-validated:** current SaaS, control-plane, restaurant, finance, and domain products.

### React — Primary

Used for complex product surfaces including:

- admin systems
- authenticated portals
- workflow-heavy operational interfaces
- configuration tools
- dashboards
- mobile-adjacent shared TypeScript patterns

My preference is to keep React responsible for interaction and presentation while authoritative business rules remain in application/domain/service boundaries.

### REST/API architecture — Primary

Hands-on work includes:

- resource and command-oriented endpoints
- typed request/response contracts
- authentication and authorization
- validation
- scoped resource access
- integration APIs
- idempotency
- error contracts
- versioning and migration concerns
- mobile/backend synchronization

**Public example:** [POS payment routes](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt).

---

## Relational data and persistence

### PostgreSQL — Primary

PostgreSQL is my strongest current database area.

Private source inspection verifies:

- Prisma schemas using the PostgreSQL provider
- application data models
- migrations and migration validation
- PostgreSQL integration tests
- relational constraints
- authorization/data-boundary work
- control-plane and domain persistence
- database operational tooling

I do not misattribute this experience to ECCB; the public ECCB repository uses MySQL/MariaDB.

**Evidence type:** private source-validated.

### Prisma — Primary

Used across current TypeScript systems for:

- relational schema design
- migrations
- generated typed clients
- transactions
- application service persistence
- PostgreSQL and MySQL/MariaDB-backed applications

**Public evidence:** ECCB uses Prisma with MySQL/MariaDB.

**Private source-validated:** multiple PostgreSQL systems.

### PostGIS — Working

Used in a private PostgreSQL-backed domain system for spatial data and geometry-backed application models.

This is real schema/application work, but I position PostGIS below general PostgreSQL depth.

**Evidence type:** private source-validated.

### MySQL / MariaDB — Working

Used in public and private application systems.

**Public evidence:**

- [ECCB Prisma schema](https://github.com/dthompsonfl/eccb.app/blob/main/prisma/schema.prisma) uses the MySQL provider.
- Repair Portal is built on Frappe/ERPNext and MariaDB/MySQL.
- The private restaurant platform also uses a MariaDB/MySQL Prisma data layer.

### Room — Primary for native Android persistence

Used for local Android persistence, explicit schema migrations, offline projections, and durable synchronization queues.

**Public evidence:**

- [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt)
- [PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt)

### MongoDB — Adjacent

I understand document-database modeling concepts and can work in an existing MongoDB system, but I do **not** present MongoDB as equivalent to my PostgreSQL experience.

No portfolio claim depends on MongoDB expertise.

---

## Authentication, authorization, and security

### RBAC / server-side authorization — Primary

I design role/permission systems with the server as the authority.

Relevant concerns include:

- role and permission modeling
- resource scope
- tenant/location boundaries
- deny-by-default behavior
- manager/admin overrides
- session authority
- auditability
- separation between UI guidance and actual authorization

**Public evidence:** ECCB contains Better Auth configuration and role/permission surfaces; Repair Portal contains Frappe permission handlers.

**Private source-validated:** centralized RBAC and authorization across current restaurant and SaaS/control-plane systems.

### Better Auth — Working

**Public evidence:** [ECCB auth configuration](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts) includes Prisma persistence, email/password flows, verification, magic links, two-factor support, database sessions, secure cookie configuration, and built-in rate-limiting configuration.

### Security engineering — Working / cross-cutting

Recurring work includes:

- secret isolation
- request validation
- session/auth boundaries
- least privilege
- scoped authorization
- webhook verification
- audit controls
- dependency/release checks
- sensitive-data boundaries
- fail-closed runtime configuration

I do not claim a compliance certification merely because a codebase contains security controls.

---

## Jobs, workflow orchestration, and reliability

### Redis — Working

Used for queue/workflow infrastructure and application coordination.

**Public evidence:** ECCB job infrastructure uses Redis/ioredis.

### BullMQ — Working

Used for named queues, workers, retries, concurrency controls, dead-letter handling, queue status, and graceful shutdown.

**Public evidence:** [ECCB queue implementation](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts).

### WorkManager — Primary for Android background work

Used for durable Android synchronization and retry behavior.

**Public evidence:** [POS SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt).

### Idempotency / retries / recovery — Primary engineering area

These are not standalone libraries; they are recurring system-design concerns.

My work includes:

- stable operation identity
- scoped idempotency
- retry classification
- outbox patterns
- conflict states
- unknown outcomes
- reconciliation
- state machines
- fail-closed integration behavior
- stale-client handling
- recovery-oriented UX

See [Reliability Patterns](./RELIABILITY_PATTERNS.md).

---

## Mobile

### Kotlin — Primary for native mobile

My strongest native application implementation evidence is Kotlin.

**Public evidence:** [dthompsonfl/pos](https://github.com/dthompsonfl/pos).

### Jetpack Compose — Primary for current Android UI work

Used for touch-oriented POS and restaurant application surfaces.

**Public evidence:** POS feature modules include Compose-based restaurant, checkout, KDS, shifts, settings, and related operational screens.

### Room — Primary

See relational/persistence section above.

### WorkManager — Primary for durable background synchronization

See jobs/workflow section above.

### React Native / Expo — Working, secondary to Kotlin

Current private source contains a first-party React Native/Expo application with:

- native routing/navigation
- API integration
- local storage
- synchronization
- network-aware behavior
- mobile product surfaces

I do not present React Native as equal to my Kotlin/Android depth.

**Evidence type:** private source-validated.

---

## Payments and commerce

### Shopify — Working / historical foundation

My software-development path began in **2021** with Shopify work, including:

- themes
- storefront customization
- apps and extensions
- product/catalog workflows
- commerce integrations
- ERP synchronization

The public [shopifysync1](https://github.com/dthompsonfl/shopifysync1) repository is a thin historical artifact and is not used as proof of current backend depth.

### Stripe — Working / substantial integration experience

Work includes:

- server-side payment operations
- PaymentIntent-oriented flows
- refunds
- webhook signature verification
- provider abstraction
- idempotency
- payment-state/reconciliation design
- private production-system integration work

**Public evidence:** [POS PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) and [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt).

**Qualification:** the public POS does not prove a completed/certified real Stripe Terminal reader implementation.

### Square — Working integration area

I have current restaurant-system work around Square integration boundaries and configuration.

**Public POS qualification:** the public Square payment module should not be read as a completed production Square payment implementation; simulated/scaffolded behavior remains.

**Private source-validated:** broader current Square integration work exists in the restaurant platform.

### POS / KDS / restaurant systems — Primary domain specialization

Hands-on engineering includes:

- ordering
- service modes
- registers
- menus/catalogs
- modifiers and pricing
- payments
- kitchen/KDS
- fulfillment
- shifts
- employees/permissions
- devices
- reporting/reconciliation
- offline/recovery design

See [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md).

---

## Enterprise and operational application platforms

### Frappe — Working

Used for Python-based operational applications, DocTypes, lifecycle hooks, permission boundaries, and scheduled processes.

**Public evidence:** [repair_portal](https://github.com/dthompsonfl/repair_portal).

### ERPNext — Working

Used as an operational/business application platform and integration target.

### Admin / control-plane architecture — Primary

A recurring area of my work is building interfaces and services for:

- settings/configuration
- permissions
- operational exceptions
- lifecycle controls
- integrations
- system health
- data governance
- owner/admin workflows

The focus is not only dashboard UI; it is the authoritative services and control boundaries behind the interface.

---

## AI, LLMs, and agent-oriented systems

### AI / LLM integrations — Working

Private source validates application-owned LLM/provider integration involving:

- provider-neutral interfaces
- OpenAI-compatible HTTP APIs
- bounded prompt/input handling
- bounded output handling
- timeouts
- transient-only retry policy
- structured JSON-schema output support
- text, vision, transcription, and image-capability boundaries
- local-versus-external provider configuration

I treat the model as a probabilistic component inside application-owned constraints.

### Agent-oriented workflows — Working

Experience includes designing systems where AI can interpret, propose, research, or coordinate work while deterministic services retain authority over:

- permissions
- business invariants
- irreversible transitions
- money movement
- persistence
- auditability

I do not use “agent” to imply that critical state is delegated to unconstrained model judgment.

---

## Developer platform and delivery

### Linux / Ubuntu — Working

Daily development and server/operations environment experience includes Linux/Ubuntu tooling, services, shell workflows, and application deployment/runtime troubleshooting.

### Git / GitHub — Primary workflow tools

Used for:

- source control
- review
- branch and release workflows
- repository automation
- CI gates
- issue/PR-driven engineering
- multi-repository product work

### CI/CD and release controls — Working / substantial

Current systems include validation gates around combinations of:

- type checking
- linting
- unit/integration/e2e tests
- schema/migration checks
- source integrity
- route/API contracts
- security checks
- build verification
- release evidence

I distinguish “a repository has a CI check” from “a production environment has been fully certified.”

### Turborepo — Working

Used in private multi-application/package repositories for workspace orchestration and shared packages.

### Testing — Primary engineering practice

Tools and patterns used across current work include:

- Vitest
- Playwright
- Node test runner
- Kotlin/JVM tests
- repository-specific contract and invariant checks
- migration verification
- source/static gates
- failure-path testing

---

## Depth summary

| Area | Depth | Evidence |
|---|---|---|
| TypeScript | **Primary** | Public + private |
| Node.js | **Primary** | Public + private |
| Next.js | **Primary** | Public + private |
| React | **Primary** | Public + private |
| REST/API architecture | **Primary** | Public + private |
| PostgreSQL | **Primary** | Private source-validated |
| Prisma | **Primary** | Public + private |
| MySQL/MariaDB | **Working** | Public + private |
| PostGIS | **Working** | Private source-validated |
| MongoDB | **Adjacent** | Explicitly not claimed as equivalent to PostgreSQL |
| Kotlin | **Primary native** | Public + private |
| Jetpack Compose | **Primary native** | Public + private |
| Room | **Primary native persistence** | Public + private |
| WorkManager | **Primary Android background work** | Public + private |
| React Native / Expo | **Working** | Private source-validated; secondary to Kotlin |
| RBAC / server authorization | **Primary** | Public + private |
| Better Auth | **Working** | Public |
| Redis | **Working** | Public + private |
| BullMQ | **Working** | Public |
| Shopify | **Working / historical foundation** | Historical public + broader work history |
| Stripe | **Working / substantial integration** | Public + private |
| Square | **Working integration area** | Public limited + private broader work |
| Python | **Working** | Public |
| Frappe / ERPNext | **Working** | Public |
| AI / LLM integration | **Working** | Private source-validated |
| Agent-oriented workflows | **Working** | Private source-validated |
| Linux / Ubuntu | **Working** | Development/operations practice |
| Git / GitHub | **Primary workflow** | Portfolio and project history |
| CI/CD / release controls | **Working / substantial** | Public + private |
| Turborepo | **Working** | Private source-validated |

## What I intentionally do not claim

- I do not claim software-development experience before **2021**.
- I do not convert earlier operating experience into software-engineering years.
- I do not present MongoDB as equivalent to PostgreSQL depth.
- I do not present React Native as equivalent to my Kotlin/Android depth.
- I do not describe simulated provider code as a completed live integration.
- I do not describe source completeness as proof of hardware, provider, deployment, or scale certification.
- I do not claim a technology is “expert” merely because it appears in a dependency file.

The goal of this stack document is to make the depth boundary obvious enough that a technical reviewer can trust the rest of the portfolio.

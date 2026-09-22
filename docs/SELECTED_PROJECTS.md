# Selected Projects

This document is intentionally status-aware. A repository can demonstrate substantial engineering without proving that every external provider, physical device, deployment environment, or scale characteristic has been validated in production.

## Status language used here

- **Implemented** — source exists for the described behavior.
- **Verified in repository** — source, tests, or repository verification material directly supports the claim.
- **Simulated** — behavior intentionally uses a simulator or fake provider rather than a production integration.
- **Scaffolded** — interfaces, routes, or structure exist, but the production implementation is incomplete.
- **In progress** — active implementation/hardening work remains.
- **External validation required** — source alone cannot prove the real provider, hardware, network, deployment, or field behavior.

---

## Enterprise POS

**Repository:** [dthompsonfl/pos](https://github.com/dthompsonfl/pos)  
**Visibility:** Public  
**Current status:** **Active prototype / production-hardening work; not represented as fully production-ready**

### Purpose

A multi-module Android/Kotlin point-of-sale system covering restaurant and retail workflows, including order entry, checkout, kitchen display, restaurant floor/table flows, shifts, customers, inventory, payments, synchronization, and hardware boundaries.

### Technology represented in source

- Kotlin
- Jetpack Compose
- Room
- WorkManager
- Kotlin coroutines / Flow
- Hilt
- Ktor backend routes
- Stripe server-side integration code
- provider-agnostic payment interfaces
- ESC/POS transport abstractions and implementations

### Engineering responsibilities represented

The repository reflects work across:

- domain and module boundaries
- persistent local data
- explicit database migrations
- sync/outbox design
- payment routing
- checkout orchestration
- tenant/store/register context
- KDS projection and kitchen actions
- restaurant order-start modes
- shift closeout and reporting flows
- hardware abstraction
- release-state documentation

### Concrete implementation evidence

**Persistence and offline/sync**

- [SyncOutboxEntity.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncOutboxEntity.kt) defines a Room-backed outbox with status, retry metadata, scoped entity information, and a unique idempotency-key index.
- [SyncEngine.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) implements WorkManager-based draining, retries/backoff, and conflict handling.
- [PosMigrations.kt](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/db/PosMigrations.kt) contains explicit Room schema migrations, including creation of the sync outbox and its unique idempotency index.

**Payments**

- [PaymentRouter.kt](https://github.com/dthompsonfl/pos/blob/main/payment-api/src/main/java/com/enterprise/pos/payment/router/PaymentRouter.kt) provides an application-level provider boundary.
- [PaymentRoutes.kt](https://github.com/dthompsonfl/pos/blob/main/backend/src/main/kotlin/com/enterprise/pos/backend/routes/PaymentRoutes.kt) requires authenticated routes and merchant/store/register context, scopes supplied idempotency keys, verifies Stripe webhook signatures, and validates payment context before capture/refund/lookup.
- [StripePaymentProvider.kt](https://github.com/dthompsonfl/pos/blob/main/payment-stripe/src/main/java/com/enterprise/pos/payment/stripe/StripePaymentProvider.kt) separates simulated behavior from the real-mode bridge and fails closed when the real Stripe Terminal bridge is not available.
- [CheckoutViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-sales/src/main/java/com/enterprise/pos/feature/sales/state/CheckoutViewModel.kt) coordinates order amount due, cash/card/provider selection, split tenders, duplicate-processing protection, and persisted payment completion.

**Restaurant operations**

- [FloorScreen.kt](https://github.com/dthompsonfl/pos/blob/main/feature-restaurant/src/main/java/com/enterprise/pos/feature/restaurant/screen/FloorScreen.kt) exposes dine-in, self-seated, takeout, delivery, retail, table selection, and floor-state UX.
- [KdsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-kds/src/main/java/com/enterprise/pos/feature/kds/state/KdsViewModel.kt) projects open kitchen tickets, routes line items by kitchen key, tracks urgency, and performs ready/served/recall state changes through the order repository.
- [ShiftsViewModel.kt](https://github.com/dthompsonfl/pos/blob/main/feature-shifts/src/main/java/com/enterprise/pos/feature/shifts/state/ShiftsViewModel.kt) handles shift open/close flows and invokes Z-report and tip-pool generation.
- [EscPosPrinter.kt](https://github.com/dthompsonfl/pos/blob/main/hardware/src/main/java/com/enterprise/pos/hardware/escpos/EscPosPrinter.kt) contains USB, Bluetooth, network, and simulated ESC/POS transports.

### Important limitations

The same repository also provides reasons **not** to overstate it:

- [README.md](https://github.com/dthompsonfl/pos/blob/main/README.md) explicitly describes the project as not production-ready.
- [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md) separates source/repository verification from remaining external validation.
- The backend sync event store is currently an in-memory development implementation rather than durable production persistence.
- Real Stripe Terminal collection requires the real bridge; the public source does not prove reader/hardware certification.
- Square and Shopify public payment paths include simulated/scaffolded behavior and are not presented here as completed production payment integrations.
- Webhook signature verification exists, but durable webhook deduplication/reconciliation is not proven by the current public backend.
- Printer transports exist in source, but source presence is not proof of every physical printer/scanner/drawer/network combination.
- No claim is made here about demonstrated production throughput or load characteristics.

### Why this project matters

This repository is useful evidence because it exposes both implementation and unfinished edges. It demonstrates restaurant/POS domain decomposition, mobile persistence, payment boundaries, state transitions, offline thinking, and failure-aware design without requiring the reader to accept a false production-readiness claim.

---

## Private Restaurant Operating Platform

**Project name:** Sanitized private restaurant operating platform  
**Visibility:** Private  
**Current status:** **Active implementation and hardening**

### Purpose

An integrated restaurant system spanning customer ordering, POS, kitchen workflows, operational administration, employee/role controls, payments, devices, and supporting business workflows.

### Source-validated technology areas

Private source inspection validates work involving:

- TypeScript and Node.js
- Next.js and React
- Prisma
- MariaDB/MySQL
- centralized authentication and RBAC
- POS checkout services and typed contracts
- KDS control-plane and native kitchen workflows
- Stripe and Square integration boundaries
- Android/Kotlin application surfaces
- local persistence, outbox, and offline capability classification
- release, migration, and security verification controls

### Engineering responsibilities represented

The work spans end-to-end system ownership:

- domain and contract design
- authoritative backend services
- POS and KDS workflows
- payment boundaries
- admin/control-plane behavior
- role/permission policy
- device/runtime concerns
- offline and recovery semantics
- verification and release gates

### Sanitized source evidence

The private codebase contains actual typed POS checkout routes, restaurant order commands, KDS contracts, centralized RBAC implementation, Android offline/outbox code, and provider integration boundaries. It also contains explicit evidence matrices identifying scenarios that still require real hardware, network-loss, provider, or device certification.

Those details are summarized here without publishing proprietary code, internal paths, customer data, credentials, production URLs, or business-sensitive configuration.

### Important limitations

This project is not used to imply that every external scenario is already certified. Current source documentation still separates code-level completion from field validation for selected hardware, network-loss, payment-provider, and device behaviors.

### Why this project matters

It is the strongest representation of my combination of restaurant operations knowledge and software engineering: workflows are designed around what cashiers, kitchen staff, managers, owners, devices, and external providers actually do when the system is under pressure.

---

## ECCB Platform

**Repository:** [dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app)  
**Visibility:** Public  
**Current status:** **Active application codebase**

### Purpose

A web platform with separate public, authenticated member, and administrative application surfaces.

### Technology verified in current source

- Next.js 16
- React 19
- TypeScript
- Prisma
- MySQL/MariaDB datasource
- Better Auth
- Redis
- BullMQ
- Vitest
- Playwright

The database distinction is intentional: this repository is **MySQL/MariaDB evidence**, not PostgreSQL evidence.

### Concrete implementation evidence

- [package.json](https://github.com/dthompsonfl/eccb.app/blob/main/package.json) verifies the current application/runtime dependencies and validation scripts.
- [src/lib/auth/config.ts](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts) implements Better Auth with Prisma persistence, password/email flows, magic links, two-factor support, secure cookie configuration, database sessions, and rate-limiting configuration.
- [src/lib/auth/permission-constants.ts](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/permission-constants.ts) exposes typed permission vocabulary used by role-aware application code.
- [src/lib/jobs/queue.ts](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts) implements Redis/BullMQ queues, queue events, worker creation, retry exhaustion handling, dead-letter movement, and graceful shutdown.
- Public source also contains member-route smoke coverage and admin role/permission management surfaces.

### Important limitations

Source inspection proves the code exists; it does not by itself certify a particular production deployment, workload, SLO, or operational scale. This portfolio therefore uses ECCB to demonstrate application architecture, auth/RBAC, data access, jobs, and multi-surface product engineering—not unsupported production-scale claims.

### Why this project matters

It demonstrates breadth beyond restaurant systems while still showing the same engineering concerns: user boundaries, authorization, background work, structured persistence, test gates, and operational administration.

---

## Repair Portal

**Repository:** [dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal)  
**Visibility:** Public  
**Current status:** **Operational-domain application codebase**

### Purpose

A Frappe/ERPNext application modeling instrument-repair operations from intake through inspection, repair/setup, QA, service planning, and related business workflows.

### Technology represented

- Python
- Frappe
- ERPNext
- MariaDB/MySQL
- Frappe DocTypes and lifecycle hooks
- scheduler/background automation
- role-based permission hooks

### Concrete implementation evidence

- [repair_portal/hooks.py](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/hooks.py) registers fixtures, role-aware routes, DocType permission handlers, document lifecycle hooks, hourly jobs, and daily jobs.
- [compliance.py](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/utils/compliance.py) implements retention-driven anonymization for completed repair records.
- The daily scheduler in hooks.py actually registers that anonymization function.
- [repair_order.py permission logic](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/permissions/repair_order.py) is one example of source-level role/permission enforcement.

### Important limitations

The public repository contains extensive documentation, some of which uses aspirational readiness language. This portfolio relies on inspected source for material claims rather than treating README language as deployment proof.

### Why this project matters

It demonstrates operational-domain modeling outside the TypeScript ecosystem and shows a recurring theme in my work: translating real business processes into explicit entities, permissions, automation, and retention behavior.

---

## Shopify & Commerce Engineering

**Representative repository:** [dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1)  
**Visibility:** Public  
**Timeline:** software development began in **2021**  
**Current status:** **Early historical artifact; not representative of current engineering depth**

### Purpose and chronology

Shopify was the starting point of my software-development path. My work expanded from themes and storefront customization into apps/extensions, product/catalog workflows, commerce integrations, ERP synchronization, and business-specific e-commerce processes.

### What the public repository actually proves

The current public repository is extremely thin: its README identifies a Shopify-to-ERPNext synchronization direction, but it does not expose enough implementation source to substantiate detailed architecture claims.

I therefore use it only as public chronology evidence.

### Why this project still matters

The early commerce work established concerns that remain central to my backend work today:

- canonical product and pricing state
- synchronization across system boundaries
- third-party API uncertainty
- idempotent/recoverable business workflows
- operator-facing tools rather than purely technical integrations

---

## Sanitized Private SaaS and Control-Plane Systems

**Project name:** Private SaaS, control-plane, finance, and domain platforms  
**Visibility:** Private  
**Current status:** **Active implementation / hardening varies by system**

### Source-validated technology areas

Current private source provides evidence for:

- TypeScript
- Node.js
- Next.js / React
- PostgreSQL
- Prisma
- PostGIS
- authentication and RBAC
- migration/release controls
- Stripe integration work
- API/service boundaries
- multi-role administrative systems
- React Native / Expo
- offline synchronization
- AI/LLM provider integration
- structured validation and repository quality gates

PostgreSQL is not inferred from ECCB. It is validated separately in private systems with Prisma schemas using the PostgreSQL provider, PostgreSQL-specific integration tests and operational tooling, and application code built around relational authority.

PostGIS is validated in a private domain system whose schema uses PostGIS geometry fields for spatial data.

Current React Native/Expo code is also present in private source, including native navigation, local storage/sync, and API integration. I still position that work below my Kotlin/Android depth.

AI/LLM source includes an application-owned provider interface and an OpenAI-compatible HTTP adapter with bounded responses, timeout handling, transient-only retries, and structured-output support.

### Privacy boundary

These systems are represented only at the capability level. No proprietary source, secrets, customer/employee information, private URLs, financial records, internal identifiers, or security-sensitive production configuration is reproduced here.

### Why these projects matter

They show that the public repositories are not the ceiling of my current work. They validate current backend/platform experience in PostgreSQL, control-plane architecture, modern TypeScript systems, mobile synchronization, and AI integrations while preserving the confidentiality of active systems.

---

## What I want a reviewer to take away

The portfolio is not intended to imply that every repository is complete or deployed at massive scale.

It is intended to show a consistent engineering trajectory:

~~~text
2021 commerce development
        |
        v
integrations and business applications
        |
        v
full-stack systems
        |
        v
backend/platform ownership
        |
        v
restaurant operations + POS/KDS/payments
        |
        v
reliability, mobile, automation, and AI boundaries
~~~

The most important recurring theme is **operational correctness**: explicit state, authoritative business rules, server-side authorization, idempotency, failure recovery, and interfaces that help real operators understand what the system is doing.

# Selected Projects

This document summarizes representative engineering work.

I distinguish between **public implementation evidence**, which can be inspected directly, and **private active systems**, where I publish sanitized architecture rather than proprietary source.

## Enterprise POS Android

**Repository:** [dthompsonfl/pos](https://github.com/dthompsonfl/pos)  
**Visibility:** public  
**Status:** active prototype / production-hardening work; not represented as fully production-ready

A multi-module Android/Kotlin point-of-sale system for restaurant and retail workflows.

### Engineering scope

- Kotlin
- Jetpack Compose
- Room persistence
- WorkManager/background synchronization
- payment-provider abstractions
- hardware abstractions
- Ktor backend module
- register-scoped order flows
- shifts and reporting
- migrations
- sync/recovery design
- release safeguards
- CI wiring

### Public reliability evidence

The repository contains direct examples of:

- [offline local writes plus a sync outbox](https://github.com/dthompsonfl/pos/blob/main/SYNC.md)
- [idempotency requirements for payment retries](https://github.com/dthompsonfl/pos/blob/main/PAYMENTS.md)
- merchant/store/register-scoped payment context and idempotency documented in [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md)
- Room database persistence and migration handling
- release behavior that fails closed when a real payment or hardware integration is unavailable

### Why it matters

The project demonstrates the distinction between:

- code that compiles
- development simulations
- integration scaffolding
- functionality that has actually been hardened for release

I consider that distinction essential in payment and operational software.

---

## Restaurant Operations Platform

**Source:** private active system  
**Public documentation:** [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md)  
**Status:** active engineering; private source intentionally not published

An integrated restaurant operating platform spanning:

- POS
- kitchen display workflows
- ordering
- menu and pricing management
- payments
- employee sessions and permissions
- shifts
- operational devices
- administrative controls
- recovery workflows

### My scope

I work across:

- requirements discovery
- architecture
- domain modeling
- backend services
- application workflows
- integrations
- mobile/device concerns
- payment boundaries
- operational UI
- role and permission design
- testing
- production-hardening decisions

### Engineering emphasis

A recurring design goal is to keep business rules canonical across POS, administration, kitchen, and mobile surfaces.

Important concerns include:

- explicit order/payment state
- safe retries
- recovery from partial failure
- permission-sensitive overrides
- minimizing operator cognitive load
- reducing repeated manual intervention

Because the production source is private, the public case study clearly separates implemented context from generalized patterns.

---

## Emerald Coast Community Band Platform

**Repository:** [dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app)  
**Visibility:** public  
**Status:** active full-stack application

A platform with public, authenticated member, and administrative surfaces.

### Stack

- Next.js 16
- React 19
- TypeScript
- Prisma
- Better Auth
- Redis
- BullMQ
- Vitest

### Capabilities

- public content
- member portal
- role-based administration
- event workflows
- music/document management
- communications
- CMS
- reporting
- background jobs
- optional OCR-assisted ingestion

### Public implementation evidence

The repository includes:

- Better Auth configuration and route handling
- Redis/BullMQ background job infrastructure
- Prisma-backed application data
- distinct public/member/admin application surfaces
- server-side application services and workers

### Why it matters

This project demonstrates full-stack architecture with multiple trust levels and user roles rather than a single public frontend.

---

## Repair Portal

**Repository:** [dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal)  
**Visibility:** public  
**Status:** operational-domain application

A Frappe/ERPNext application for instrument-repair operations.

### Workflow

```text
intake
  -> inspection
  -> service planning
  -> repair execution
  -> materials / parts tracking
  -> QA
  -> delivery
```

### Technical focus

- Python
- Frappe/ERPNext
- structured domain entities
- configurable feature flags
- role-aware workflows
- retention controls
- operational settings
- payment/shipping integration points
- backup and go-live planning

### Public implementation evidence

The repository exposes a large operational domain model and includes implemented data-retention behavior such as the configurable `data_retention_months` path in its compliance utilities.

### Why it matters

The project demonstrates domain modeling across many related business entities and stateful workflows rather than a small CRUD application.

---

## Shopify & Commerce Engineering

**Representative repository:** [dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1)  
**Timeline:** software development began in 2021  
**Status:** early public artifact; not representative of my current engineering depth

Shopify was the starting point of my software-development path.

My commerce work included:

- storefront customization
- themes
- apps and extensions
- product/catalog behavior
- integrations
- ERP synchronization
- operational commerce workflows

That work gradually expanded into standalone applications, backend services, APIs, databases, payments, authentication, and SaaS architecture.

The public repository is useful primarily as historical evidence of that progression; its current documentation is much thinner than my recent projects.

---

## Large private SaaS and operational platforms

Several active repositories remain private because they represent production-oriented products, internal operational systems, or business-sensitive implementations.

Across these systems I have worked with combinations of:

- Next.js
- React
- TypeScript
- Node.js
- PostgreSQL
- PostGIS
- Prisma
- Stripe
- authentication and RBAC
- mobile/PWA architecture
- queues/background processing
- AI-assisted workflows
- repository and release quality gates
- administrative control planes
- tenant-aware application boundaries

I do not make a repository public simply to create recruiting evidence.

When source should remain private, I prefer to publish:

- sanitized architecture
- failure-mode analysis
- engineering principles
- non-proprietary patterns
- public implementation examples from adjacent systems

That keeps the portfolio technically useful without weakening security or confidentiality.

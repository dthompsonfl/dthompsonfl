# Selected Projects

This document summarizes representative engineering work. Public repositories are linked directly. Active commercial and operational systems remain private where source code or data should not be exposed.

## Enterprise POS Android

**Repository:** [dthompsonfl/pos](https://github.com/dthompsonfl/pos)

A multi-module Android/Kotlin point-of-sale prototype for restaurant and retail workflows.

### Engineering scope

- Kotlin and Jetpack Compose
- Room persistence
- payment-provider abstractions
- hardware abstractions
- Ktor backend module
- CI wiring
- register-scoped order flows
- shift and reporting workflows
- sync and migration scaffolding
- release-signing safeguards
- fail-closed production behavior

### What matters technically

The repository is intentionally explicit about what is and is not production-ready. Simulated payment paths, debug signing, unimplemented hardware transports, and unsafe migration behavior are prevented from silently becoming release behavior.

This project reflects an engineering principle I care about strongly: **incomplete production integrations should fail visibly and safely rather than pretend to work.**

---

## Restaurant Operations Platform

**Source:** private active system  
**Public documentation:** [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md)

An integrated restaurant operating platform spanning:

- POS
- kitchen display workflows
- ordering
- menu and pricing management
- payments
- employee sessions and permissions
- shifts
- devices
- administrative controls
- operational recovery

### My scope

I work across architecture, domain modeling, workflow design, backend services, integrations, mobile concerns, payment flows, operational UI, testing, and production-hardening decisions.

A recurring design goal is to keep business rules canonical across POS, administration, kitchen, and mobile surfaces instead of allowing each client to invent its own interpretation of state.

---

## Emerald Coast Community Band Platform

**Repository:** [dthompsonfl/eccb.app](https://github.com/dthompsonfl/eccb.app)

A full-stack platform with public, authenticated member, and administrative surfaces.

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

This project demonstrates my preference for separating public, authenticated, and administrative concerns while keeping shared business logic and permissions explicit.

---

## Repair Portal

**Repository:** [dthompsonfl/repair_portal](https://github.com/dthompsonfl/repair_portal)

A Frappe/ERPNext domain application for instrument-repair operations.

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

The project is useful evidence of domain modeling: it contains many related business entities and stateful workflows rather than functioning as a simple CRUD demonstration.

---

## Shopify & Commerce Engineering

**Representative repository:** [dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1)

Shopify was the starting point of my software-development path in 2021.

My commerce work has included:

- storefront customization
- themes
- apps and extensions
- product/catalog behavior
- integrations
- ERP synchronization
- operational commerce workflows

That work gradually expanded into standalone applications, backend services, APIs, databases, payments, authentication, and SaaS architecture.

---

## Large Private SaaS Platforms

Several active repositories remain private because they represent production-oriented products or internal operational systems.

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

I prefer to keep private production code private and publish sanitized architecture or patterns when that provides useful technical evidence without exposing business-sensitive implementation details.

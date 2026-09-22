# Technical Stack

This is a practical inventory of technologies I currently use or have used in hands-on project work.

I separate areas of stronger day-to-day depth from technologies I have worked around but would not present as my primary expertise.

## Primary application stack

### TypeScript / JavaScript

Used across:

- backend application logic
- APIs
- Next.js applications
- React interfaces
- validation
- integrations
- workflow logic
- administrative systems
- automation

### Node.js

Used for:

- backend services
- API endpoints
- application orchestration
- integration boundaries
- background/server workflows
- TypeScript service layers

### Next.js / React

Used for:

- public applications
- authenticated portals
- administrative control planes
- SaaS interfaces
- operational dashboards
- server/client application boundaries

Recent work uses modern App Router-era Next.js and React.

### PostgreSQL

A primary database technology in current full-stack and SaaS work.

Areas include:

- relational domain modeling
- schema evolution
- migrations
- transactional business data
- authorization context
- tenant/location relationships
- reporting-oriented structures
- integrity constraints

### Prisma

Used for:

- schema definition
- generated database clients
- migrations
- relational application access
- service-layer persistence

### PostGIS

Used where applications require geospatial data and spatial operations.

---

## Mobile and offline systems

### Kotlin / Jetpack Compose

Used for Android operational applications, including POS-oriented work.

### Room

Used for structured Android persistence, schema versioning, migrations, and local operational data.

### WorkManager

Used in mobile architecture for durable/background work patterns.

### React Native

Part of my cross-platform mobile architecture work. My strongest current native implementation evidence is Android/Kotlin; I do not present myself as primarily a React Native specialist.

---

## Authentication and authorization

Experience includes:

- role-based access control
- permission models
- tenant-aware boundaries
- administrative roles
- server-side authorization
- session-aware application flows
- Better Auth
- application-specific identity systems

I treat client-side visibility and server-side authorization as separate concerns.

---

## Payments and commerce

### Shopify

My software-development path began in 2021 with Shopify work, including:

- themes
- storefront customization
- apps/extensions
- integrations
- product/catalog workflows
- ERP synchronization

### Stripe

Used in commerce/SaaS/payment architecture and application integrations.

### Square

Used in restaurant/POS integration work and payment architecture.

For card-present and other high-consequence payment paths, I prefer explicit provider state, idempotency, reconciliation, and fail-closed behavior when a real provider integration is unavailable.

---

## Background work and messaging

### Redis / BullMQ

Used in full-stack platforms for cache/queue/background-job concerns.

### Durable workflow concepts

My design work includes:

- retries
- idempotency
- outbox-style thinking
- reconciliation
- scheduled/background jobs
- explicit workflow state

I choose the specific technology based on system needs rather than forcing every workflow through one framework.

---

## Python / Frappe / ERPNext

Earlier business-application work includes:

- Python
- Frappe
- ERPNext
- DocTypes/domain modeling
- operational workflows
- configuration
- integrations

The public [Repair Portal](https://github.com/dthompsonfl/repair_portal) is the clearest example.

---

## AI and automation

Hands-on interests and project work include:

- LLM integrations
- structured model output
- tool-using workflows
- retrieval/context systems
- AI-assisted operational workflows
- human-review boundaries
- agent orchestration concepts

My preferred architecture keeps critical invariants deterministic even when AI participates in reasoning or classification.

---

## Testing and quality

Technologies and practices include combinations of:

- Vitest
- TypeScript type checking
- linting
- repository quality gates
- migration validation
- API contract checks
- security-focused tests
- integration tests
- regression tests
- build validation

I focus most heavily on tests that protect business invariants and failure modes.

---

## Infrastructure and delivery

Experience includes:

- Linux/Ubuntu environments
- CI/CD workflows
- Git/GitHub
- monorepos
- Turborepo
- environment contracts
- release validation
- production-readiness checklists
- self-hosted application deployment patterns

---

## Technologies I would not overstate

### MongoDB

My strongest database depth is relational, especially PostgreSQL.

I understand document-database concepts and can work in an existing MongoDB system, but I do **not** present MongoDB as equivalent to my PostgreSQL experience.

That distinction matters to me because a technical profile should make strengths and learning areas clear rather than turning every technology touched into an "expert" keyword.

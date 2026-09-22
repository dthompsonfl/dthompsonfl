# Technical Stack

This is a practical inventory of technologies I currently use or have used in hands-on project work.

I separate strong day-to-day areas from adjacent technologies so the profile is useful during a technical interview rather than functioning as a keyword list.

## Depth legend

- **Primary** — used regularly in current engineering work
- **Working** — meaningful hands-on use, but not my primary specialization
- **Adjacent** — comfortable with the architecture/concepts; less production depth

## Backend and application development

### TypeScript — Primary

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

TypeScript is currently the language most consistently shared across my web/backend application work.

### Node.js — Primary

Used for:

- backend services
- API endpoints
- application orchestration
- integration boundaries
- server-side workflows
- TypeScript service layers

### JavaScript — Primary/Working

Used throughout earlier and current web/application work, including Shopify and Frappe/browser integrations.

## Web application architecture

### Next.js — Primary

Used for:

- public applications
- authenticated portals
- administrative control planes
- SaaS interfaces
- operational dashboards
- API/server functionality
- server/client boundaries

Recent work uses modern App Router-era Next.js.

### React — Primary

Used across:

- application interfaces
- administrative surfaces
- operational dashboards
- reusable component systems
- responsive applications

## Relational data

### PostgreSQL — Primary

A primary database technology in current SaaS and backend work.

Hands-on concerns include:

- relational domain modeling
- schema evolution
- migrations
- transactional business data
- tenant/location relationships
- authorization context
- reporting structures
- integrity constraints

### Prisma — Primary

Used for:

- schema definition
- generated database access
- relational modeling
- migrations
- service-layer persistence
- seed workflows

### PostGIS — Working

Used where applications require:

- geospatial records
- spatial relationships
- location-aware workflows

### MariaDB / MySQL — Working

Used in Frappe/ERPNext and public full-stack application work.

The [ECCB platform](https://github.com/dthompsonfl/eccb.app) is a current public example.

## Mobile and offline systems

### Kotlin — Working

Used for Android operational applications, especially POS-oriented work.

### Jetpack Compose — Working

Used for modern Android UI and operational workflows.

### Room — Working

Used for:

- structured local persistence
- schema versioning
- migrations
- offline operational data

Public implementation: [Enterprise POS Android](https://github.com/dthompsonfl/pos).

### WorkManager — Working

Used in Android architecture for durable/background synchronization work.

### React Native — Adjacent/Working

Part of my cross-platform mobile architecture work.

My strongest current public native implementation evidence is Android/Kotlin, so I do not present myself as primarily a React Native specialist.

## Authentication and authorization

### RBAC / application authorization — Primary

Experience includes:

- role-based access control
- fine-grained permission models
- tenant-aware boundaries
- location-aware boundaries
- administrative roles
- server-side authorization
- session-aware application flows

I treat client-side visibility and server-side authorization as separate concerns.

### Better Auth — Working

Used in modern Next.js application work.

Public evidence:

- [ECCB auth configuration](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts)
- [ECCB auth route](https://github.com/dthompsonfl/eccb.app/blob/main/src/app/api/auth/%5B...all%5D/route.ts)

## Payments and commerce

### Shopify — Working / historical foundation

My software-development path began in 2021 with Shopify work, including:

- themes
- storefront customization
- apps/extensions
- integrations
- product/catalog workflows
- ERP synchronization

### Stripe — Working

Used in commerce, SaaS, and payment architecture.

Public POS evidence includes:

- server-side Stripe integration code
- payment intent/capture/refund boundaries
- scoped idempotency
- explicit separation between real and simulated payment behavior

### Square — Working/Adjacent

Used in restaurant/POS integration planning and application work.

I do not present Square integration depth as equivalent to PostgreSQL/TypeScript backend depth.

## Background work and messaging

### Redis — Working

Used for cache/queue/background-job concerns.

### BullMQ — Working

Used in full-stack application background jobs.

Public evidence exists in [ECCB](https://github.com/dthompsonfl/eccb.app).

### Workflow patterns — Primary architectural focus

My design work includes:

- retries
- idempotency
- explicit state transitions
- reconciliation
- scheduled/background work
- outbox-style delivery
- recovery paths

I choose the implementation technology based on system requirements rather than forcing every workflow into one framework.

## Python / Frappe / ERPNext

### Python — Working

Used in earlier business-application work and operational automation.

### Frappe / ERPNext — Working

Experience includes:

- DocTypes/domain modeling
- operational workflows
- configuration
- feature flags
- scheduled jobs
- integrations

Public example: [Repair Portal](https://github.com/dthompsonfl/repair_portal).

## AI and automation

### LLM integrations — Working

Hands-on work and project architecture includes:

- structured model output
- tool-using workflows
- retrieval/context systems
- AI-assisted operational workflows
- human-review boundaries
- agent orchestration concepts

My preferred architecture keeps critical invariants deterministic even when AI participates in interpretation or reasoning.

### Agentic workflows — Working/Architectural focus

I am particularly interested in systems where AI can:

- classify
- recommend
- coordinate
- invoke authorized tools
- reduce operator work

while deterministic services still enforce permissions, state transitions, and irreversible effects.

## Testing and quality

Hands-on technologies and practices include combinations of:

- Vitest
- TypeScript type checking
- linting
- unit tests
- integration tests
- security-focused tests
- migration validation
- API contract checks
- regression tests
- build validation
- repository quality gates

I focus most heavily on tests that protect business invariants and high-cost failure modes.

## Infrastructure and delivery

### Linux / Ubuntu — Primary working environment

Used for development, servers, deployment, troubleshooting, and local tooling.

### Git / GitHub — Primary

Used for:

- source control
- branch workflows
- pull requests
- code review
- automation
- CI/CD

### Monorepos / Turborepo — Working

Used in multi-application SaaS and platform architectures.

### CI/CD — Working

Used for:

- build validation
- test execution
- release checks
- repository integrity
- migration/contract gates

## Domain-focused technical experience

### Restaurant and POS systems

Hands-on architecture/application work includes:

- orders
- POS/register workflows
- KDS
- menu/catalog
- modifiers/pricing
- payments
- employee permissions
- shifts
- hardware boundaries
- offline persistence
- synchronization
- operational recovery

### SaaS control planes

Work includes:

- admin portals
- customer portals
- tenant-aware access
- role/permission systems
- billing/integration boundaries
- centralized configuration

### Commerce

Work includes:

- Shopify
- catalog/product flows
- payment integration
- commerce-to-ERP synchronization

## Technologies I would not overstate

### MongoDB — Adjacent

My strongest database depth is relational, especially PostgreSQL.

I understand document-database concepts and can work in an existing MongoDB system, but I do **not** present MongoDB as equivalent to my PostgreSQL experience.

That distinction matters because a technical profile should make strengths and learning areas clear instead of turning every technology encountered into an "expert" keyword.

## Current strongest fit

The roles that best match my present engineering profile are those combining:

- TypeScript/Node.js backend work
- relational data
- operational workflows
- integration boundaries
- reliability
- restaurant/small-business systems
- end-to-end product ownership

That is where my current technical depth and prior operational experience reinforce each other most directly.

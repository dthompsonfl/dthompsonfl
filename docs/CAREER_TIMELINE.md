# Career & Engineering Timeline

My software-development timeline begins in **2021**.

My career before and alongside software includes **more than a decade of hands-on business operations**. I do **not** count those operating years as software-engineering tenure. I do treat them as domain experience because they materially affect how I design employee workflows, cash and reconciliation controls, customer-facing processes, exception handling, and software for nontechnical operators.

## Progression at a glance

~~~text
business and operations experience
        |
        v
2021: Shopify development
        |
        v
commerce integrations and ERP workflows
        |
        v
business applications and operational software
        |
        v
full-stack web systems
        |
        v
backend and platform engineering
        |
        v
restaurant systems, SaaS, mobile, reliability, AI/automation
~~~

## Before 2021 — operating context, not engineering tenure

Before software became a primary technical discipline for me, I had already spent years working directly with customer-facing business operations.

That operating experience includes:

- employee workflows
- staffing and scheduling
- cash controls
- financial reconciliation
- customer operations
- SOPs and repeatable processes
- exception handling
- high-pressure operating environments
- translating business rules into steps that nontechnical employees can execute

This background is relevant to my engineering because operational systems fail in ways that are different from purely informational software. An order that is duplicated, a payment whose outcome is unknown, a stale register, a permission leak, or a kitchen workflow that cannot recover can become an immediate business problem.

The distinction remains important: **domain experience informs my engineering; it does not extend my software-development start date backward.**

## 2021 — Shopify and commerce development

I began software development in **2021** through Shopify customization and commerce work.

The work included:

- Shopify themes
- Shopify apps and extensions
- storefront customization
- product and catalog workflows
- commerce integrations
- ERP synchronization
- business-specific e-commerce workflows

A surviving public artifact is [dthompsonfl/shopifysync1](https://github.com/dthompsonfl/shopifysync1), whose public repository currently contains only a minimal Shopify-to-ERPNext description. I treat it as chronology evidence, not as proof of my present engineering depth.

### What this stage taught me

Commerce work made software consequences concrete early:

- product identity has to remain consistent across systems;
- pricing and inventory cannot be treated as decorative UI state;
- third-party APIs fail and change independently;
- synchronization requires explicit ownership and conflict behavior;
- business users need workflows, not raw data models.

## Expansion — integrations, ERP workflows, and business applications

From the initial Shopify work, my development expanded toward systems that coordinated multiple business domains.

That included deeper work with:

- backend services and APIs
- JavaScript and TypeScript
- Node.js
- Python
- Frappe/ERPNext
- relational data
- integrations between commerce and operational systems
- scheduled/background processing
- authentication and role-aware behavior

The public [Repair Portal](https://github.com/dthompsonfl/repair_portal) is one inspectable example of this stage of thinking: operational domain entities, role permissions, scheduler hooks, and configurable retention behavior are represented in source.

### Shift in engineering focus

The engineering problem stopped being “customize a storefront” and became “model a business process so that the system remains understandable when the workflow branches, fails, or requires human intervention.”

## Expansion — full-stack product systems

My work then broadened into complete application surfaces rather than isolated integrations.

Typical concerns became:

- Next.js and React application architecture
- API and service boundaries
- Prisma and relational schemas
- authentication
- authorization and RBAC
- admin and member/user surfaces
- background jobs
- Redis-backed work queues
- file/document workflows
- test and release controls

The public [ECCB application](https://github.com/dthompsonfl/eccb.app) is current inspectable evidence for this class of work. Its source includes Next.js/React/TypeScript, Prisma on MySQL/MariaDB, Better Auth configuration, role/permission surfaces, and Redis/BullMQ workers.

## Expansion — backend/platform engineering and stronger data authority

As the systems became more consequential, my focus moved increasingly toward backend and platform concerns:

- TypeScript and Node.js services
- REST/API contracts
- PostgreSQL
- Prisma
- migrations and schema safety
- authentication and server-side authorization
- multi-role and multi-surface systems
- tenant/location scoping
- idempotency
- job/workflow orchestration
- auditability
- failure recovery
- CI and release gates
- admin/control-plane architecture

Current private repositories provide the strongest source-level evidence for my PostgreSQL work. I have inspected PostgreSQL-backed Prisma schemas, PostgreSQL integration tests, migration controls, and application-level data authority in those systems. I keep those implementations private and summarize them here rather than exposing proprietary source.

## Expansion — restaurant technology and operational reliability

Restaurant systems combine most of the failure modes that pushed my engineering toward backend/platform work.

My work in this area includes:

- ordering
- POS/register workflows
- restaurant floor and service modes
- menus, modifiers, pricing, and catalog configuration
- payments
- Stripe and Square integration work
- KDS/kitchen state
- fulfillment flows
- shifts and cash-control workflows
- employee roles and permissions
- devices and hardware boundaries
- offline-aware behavior
- retry and reconciliation design
- operational admin/control planes

The public [POS repository](https://github.com/dthompsonfl/pos) exposes a meaningful subset of this work in Kotlin/Jetpack Compose, Room, WorkManager, Ktor backend scaffolding, provider abstractions, KDS, shifts, and restaurant floor flows.

Its status matters: it is an **active prototype / production-hardening repository**, not proof that every payment provider, hardware path, backend persistence layer, and production environment has been fully certified.

A more complete restaurant operating platform also exists in private source. The public [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md) uses that experience in sanitized form while keeping proprietary implementation details private.

## Expansion — native mobile and offline-aware systems

My strongest publicly inspectable native application evidence is Android/Kotlin:

- Kotlin
- Jetpack Compose
- Room
- WorkManager
- local persistence
- schema migrations
- queued synchronization
- device/hardware boundaries

I also have current private React Native/Expo implementation work, including native navigation, local storage/synchronization, and API integration. I describe React Native as a **working secondary area**, not as equivalent to my Kotlin/Android depth.

## Expansion — AI and agent-oriented systems

My AI work is application engineering around model capabilities rather than treating an LLM as an authority over core business state.

Private source validates work involving:

- provider-neutral AI boundaries
- OpenAI-compatible model adapters
- bounded inputs and outputs
- timeout and transient-retry handling
- structured response constraints
- local-versus-external provider configuration
- AI-assisted product workflows
- agent-oriented integration patterns

My preferred safety boundary is:

~~~text
model interprets or proposes
        |
        v
application policy validates
        |
        v
authorized deterministic service executes
        |
        v
system observes and records result
        |
        v
reconcile or escalate uncertainty
~~~

Money movement, permissions, irreversible state changes, and core business invariants should not depend on unconstrained model judgment.

## Current positioning

Today I am best represented as a **Backend & Platform Engineer** with end-to-end product capability and unusually direct operational-domain context.

The strongest themes across my work are:

- TypeScript / Node.js backend systems
- Next.js / React product systems
- PostgreSQL / Prisma data design
- APIs and integration boundaries
- authentication / RBAC
- restaurant technology
- POS / KDS / payments
- operational admin and control planes
- Kotlin / Android
- offline and recovery design
- workflow automation
- AI/LLM integrations inside deterministic application boundaries
- testing, security, migration, and release controls

## The experience boundary

For clarity:

- **Software development:** 2021–present
- **Earlier/parallel business operations:** more than a decade of domain experience
- **Claim I do not make:** that the operating years are software-engineering years
- **Claim I do make:** that operating real businesses has materially shaped the systems, failure modes, and user workflows I know how to design for

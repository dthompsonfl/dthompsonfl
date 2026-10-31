# Career Timeline: Operations, Business Systems & Technology

My professional background combines **more than a decade of hands-on business operations** with purchasing, pricing, service coordination, process improvement and independent technology work. I began developing software on a project basis in **2021**.

My operating experience is an important qualification in its own right, **not merely a prelude to engineering**. The timelines overlap and describe different kinds of experience. I do **not** count operating years as salaried engineering tenure.

For concrete examples, see [purchasing and supplier decisions](./case-studies/purchasing-pricing-and-supplier-decisions.md), [operational reporting and improvement](./case-studies/operational-reporting-and-process-change.md), and [business-systems implementation](./case-studies/business-systems-and-implementation.md).

## Firsthand employment and business chronology

| Work | Responsibilities and progression | Dates |
|---|---|---|
| The UPS Store | Assistant Manager: shift coordination, customer service, shipping records and escalations | August 2011–December 2014 |
| Panama City Bingo | Floor Worker, then Manager-in-Charge beginning March 2016 | October 2014–October 2018 |
| Mary Esther Bingo | Floor Worker, then Manager-in-Charge beginning July 2020 | October 2018–present |
| Specialty repair business | Instrument service, supplier purchasing, pricing, customer estimates and operating records | Business operations March 2022–August 2025 |
| Independent software work | Project-based commerce, integrations, operational apps and AI-assisted development | 2021–present |

The roles and dates above are **firsthand accounts**, not employment records independently certified by the named organizations. The periods overlap; software is not presented as continuous salaried developer employment. The repair-business trading period is distinct from the earlier self-funded technical training that began in 2021.

[People and operations](./case-studies/frontline-operations-and-team-coordination.md) · [Purchasing and pricing](./case-studies/purchasing-pricing-and-supplier-decisions.md) · [Commercial decisions](./case-studies/commercial-document-review-and-decision-support.md)

## Progression at a glance

~~~text
2011 onward: customer-facing and shift operations
        |
        +-- management, staff coordination and service improvement
        |
2021 onward: independent, project-based software development
        |
2022–2025: specialty repair business operations
        |       +-- purchasing, supplier decisions, pricing and records
        |
        +-- commerce, ERP, CRM and operational applications
                |
                +-- backend, web, mobile and AI-assisted systems work
~~~

## Operating career — direct work with people, purchasing and processes

My customer-facing operating career began long before my software work and has continued alongside it. My experience spans retail/shipping service, frontline venue work, Manager-in-Charge responsibilities and an owner-operated specialty repair business. In those settings I worked directly with the people, information and commercial decisions that keep an operation functioning.

That operating experience includes:

- shift and employee coordination
- supplier purchasing, price comparison and order records
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

Current private repositories provide the strongest source-level evidence for my PostgreSQL work. Prior private source review documented PostgreSQL-backed Prisma schemas, PostgreSQL integration tests, migration controls and application-level data authority in those systems. I keep those implementations private and summarize them here rather than exposing proprietary source.

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

Today I am best represented as an **Operations & Business Systems Professional** with hands-on purchasing, pricing, people and process experience, supported by independent technical implementation capability. My engineering work is a significant asset, but it is not the only career story.

The strongest themes across my work include:

- purchasing, supplier comparisons and pricing decisions
- shift management, service coordination and process documentation
- operational reporting, exception review and commercial judgment
- business requirements, CRM work and implementation decisions
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
- **Earlier/parallel business operations:** more than a decade of practical experience with people, processes, purchasing and customer service
- **Claim I do not make:** that the operating years are software-engineering years
- **Claim I do make:** that hands-on operations, commercial decisions and practical technical work together inform how I investigate problems and implement solutions

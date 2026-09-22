# Career & Engineering Timeline

## Summary

My software-development timeline begins in **2021**.

Before that, my career was primarily business and operations. I do **not** present those earlier years as software-engineering experience. I do use that operating background as domain context because it directly affects how I design software for employees, customers, payments, devices, and time-sensitive business workflows.

## 2013–2020 — Business and operations foundation

Before I began developing software, I spent years operating customer-facing businesses.

That work involved recurring concerns such as:

- staffing and scheduling
- customer service
- cash controls
- reconciliation
- operating procedures
- exception handling
- pricing and business rules
- vendor systems
- training people with different levels of technical ability

The biggest lesson I carried into software engineering is that an application can be technically functional while still failing operationally.

Real users are busy. Networks fail. Instructions are skipped. Devices malfunction. Financial state must still be understandable.

## 2021 — Shopify and commerce development

I began software development in **2021** through Shopify customization and development.

My early work included:

- Shopify theme customization
- storefront behavior
- app and extension work
- product and catalog workflows
- commerce integrations
- ERP synchronization
- merchant-specific business logic
- adapting third-party applications to actual operating requirements

A representative public artifact from this period is [Shopify Sync](https://github.com/dthompsonfl/shopifysync1), an early Shopify/ERP integration repository.

This stage taught me to develop in commerce environments where software changes could directly affect customers, transactions, inventory, and revenue.

## 2022–2024 — Integrations and operational applications

I progressively moved beyond storefront customization into broader application and integration work.

Areas of growth included:

- APIs and service integrations
- ERP workflows
- Python/Frappe applications
- backend domain modeling
- workflow automation
- data synchronization
- authenticated applications
- administrative tools
- operational configuration
- early AI/LLM integrations and experimentation

The public [Repair Portal](https://github.com/dthompsonfl/repair_portal) is representative of this evolution.

It models a multi-stage operational workflow:

```text
intake
  -> inspection
  -> service planning
  -> repair execution
  -> parts / materials
  -> quality assurance
  -> delivery
```

The project also includes configurable modules, operational settings, data-retention behavior, and external integration points.

## 2025 — Full-stack and platform architecture

My work expanded into larger systems with multiple user surfaces and clearer application boundaries.

I increasingly focused on:

- Next.js
- React
- TypeScript
- Node.js
- relational database design
- Prisma
- authentication and RBAC
- administrative control planes
- multi-role workflows
- background jobs and queues
- payment integrations
- mobile architecture
- automated testing
- release and migration controls

The public [Emerald Coast Community Band Platform](https://github.com/dthompsonfl/eccb.app) demonstrates several of these concerns in one application:

- public application surface
- authenticated member portal
- administrative workspace
- Better Auth
- role-based permissions
- Redis/BullMQ background jobs
- Prisma-backed data access
- content/document workflows

## 2025–2026 — Restaurant systems, SaaS, mobile, and reliability

My current work is centered on larger operational platforms, including restaurant technology and SaaS products.

Key areas include:

- POS architecture
- kitchen display workflows
- ordering
- menu and pricing configuration
- payment flows
- employee and role permissions
- shifts and tender reconciliation
- operational devices
- offline-aware mobile systems
- server-authoritative state
- workflow orchestration
- idempotency and retries
- migration safety
- release gates
- AI-enabled workflows
- administrative control planes

The public [Enterprise POS Android](https://github.com/dthompsonfl/pos) repository provides inspectable examples of this progression.

Its public documentation and source include:

- Room-backed local persistence
- an offline sync outbox
- idempotency-key handling
- payment-provider abstractions
- scoped payment context
- migration controls
- explicit release safeguards
- fail-closed behavior when production integrations are incomplete

The repository is also explicit about unfinished production work. I consider that transparency part of engineering quality.

## 2026 — Current direction

I am increasingly focused on software where backend correctness directly affects real operations.

Areas that interest me most:

- restaurant technology
- workflow orchestration
- high-consequence business processes
- payments and transaction state
- distributed operational state
- platform engineering
- autonomous/agentic systems with deterministic guardrails
- software for small and local businesses

## Experience boundary

My software-development experience begins in **2021**.

My earlier business experience adds:

- operational judgment
- domain understanding
- customer empathy
- financial-process awareness
- experience designing for nontechnical users

but I keep that distinct from years of software-development experience.

That distinction is intentional: I would rather make the timeline accurate and let the scope of the engineering work speak for itself.

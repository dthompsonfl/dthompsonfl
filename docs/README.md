# Portfolio Documentation

This directory provides technical context behind my public GitHub profile.

The goal is to give an engineering reviewer enough depth to understand how I approach backend, platform, operational, and restaurant software without exposing private production repositories, customer data, credentials, production configuration, or proprietary implementation details.

## How to read this portfolio

For a fast technical review:

1. [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md)
2. [Reliability Patterns](./RELIABILITY_PATTERNS.md)
3. [Selected Projects](./SELECTED_PROJECTS.md)
4. [Technical Stack](./TECHNICAL_STACK.md)
5. [Engineering Principles](./ENGINEERING_PRINCIPLES.md)
6. [Career & Engineering Timeline](./CAREER_TIMELINE.md)

## Evidence standard

I separate portfolio material into three categories.

### Public implementation evidence

Source that can be inspected directly in a public repository.

Examples:

- [Enterprise POS Android](https://github.com/dthompsonfl/pos)
- [Emerald Coast Community Band Platform](https://github.com/dthompsonfl/eccb.app)
- [Repair Portal](https://github.com/dthompsonfl/repair_portal)
- [Shopify Sync](https://github.com/dthompsonfl/shopifysync1)

### Sanitized private-system case studies

Architecture and decisions derived from active systems whose production source remains private.

These documents intentionally omit:

- customer and employee data
- secrets and credentials
- production URLs
- internal identifiers
- proprietary rules that should remain private
- source code copied from private repositories

### Engineering patterns

General patterns I use or prefer when designing systems. A pattern appearing in this portfolio does **not** mean every project implements it in exactly the same way.

Where a public repository demonstrates a pattern directly, I link to that evidence.

## Documents

### [Career & Engineering Timeline](./CAREER_TIMELINE.md)

How my software work progressed from Shopify development in 2021 into integrations, backend systems, SaaS, mobile, restaurant technology, workflow automation, and AI-enabled applications.

It also makes the boundary between my earlier operations career and my software-development timeline explicit.

### [Selected Projects](./SELECTED_PROJECTS.md)

Representative projects with:

- public/private status
- technical scope
- responsibilities
- implementation evidence
- current limitations where relevant
- why the project matters to my engineering profile

### [Restaurant Operations Case Study](./RESTAURANT_OPERATIONS_CASE_STUDY.md)

A sanitized case study covering:

- ordering
- POS/register state
- kitchen/KDS workflows
- payments
- menu/configuration consistency
- permissions and overrides
- offline/recovery behavior
- observability
- automation and AI boundaries

### [Reliability Patterns](./RELIABILITY_PATTERNS.md)

Patterns for:

- idempotency
- workflow state
- retries
- partial failure
- reconciliation
- outbox/event delivery
- webhooks
- offline behavior
- auditability
- business-level observability
- recovery

### [Engineering Principles](./ENGINEERING_PRINCIPLES.md)

The principles I use when designing and reviewing production-oriented systems, including canonical business rules, explicit state, server-side authorization, safe failure, maintainability, and end-to-end ownership.

### [Technical Stack](./TECHNICAL_STACK.md)

A practical inventory of technologies grouped by actual depth and usage. It explicitly distinguishes strong hands-on areas from technologies I can work with but would not claim as equivalent expertise.

## Public evidence worth inspecting

### POS reliability

- [SYNC.md](https://github.com/dthompsonfl/pos/blob/main/SYNC.md)
- [PAYMENTS.md](https://github.com/dthompsonfl/pos/blob/main/PAYMENTS.md)
- [FINAL_VERIFICATION.md](https://github.com/dthompsonfl/pos/blob/main/FINAL_VERIFICATION.md)
- [POS README](https://github.com/dthompsonfl/pos/blob/main/README.md)

### Full-stack application architecture

- [ECCB README](https://github.com/dthompsonfl/eccb.app/blob/main/README.md)
- [ECCB authentication configuration](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts)
- [ECCB project structure](https://github.com/dthompsonfl/eccb.app)

### Operational domain modeling

- [Repair Portal README](https://github.com/dthompsonfl/repair_portal/blob/main/README.md)
- [Repair Portal retention implementation](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/utils/compliance.py)

## Current portfolio boundary

Some of my strongest current engineering work is private because it is tied to active products or business operations. I do not make those repositories public solely for recruiting.

Instead, this portfolio is designed to provide:

- verifiable public code where available
- clear disclosure of current project status
- sanitized architecture where source must remain private
- honest statements about areas of depth and areas still being developed

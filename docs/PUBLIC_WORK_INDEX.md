# Public Work Index

**Purpose:** Give reviewers a broader and more accurate map of code visible under my GitHub account, organized by the problem domain rather than by stars, repository count or trendy technology.

A repository appearing under this account is not, by itself, proof that I personally wrote every line. I have used AI-assisted development and third-party libraries. Public code also does not establish customer adoption, successful deployment, scientific validity or commercial outcomes. The status descriptions below are deliberately narrower than those claims.

## Business-facing application evidence

| Repository | Public artifact to inspect | What it demonstrates | Status / limitation |
|---|---|---|---|
| [Repair Portal](https://github.com/dthompsonfl/repair_portal) | [Frappe hooks](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/hooks.py) and [permissions](https://github.com/dthompsonfl/repair_portal/blob/main/repair_portal/repair_portal/permissions/repair_order.py) | Repair intake and service-process entities, role controls and scheduled logic | Source-backed operational application; no blanket deployment certification |
| [ECCB](https://github.com/dthompsonfl/eccb.app) | [Authentication configuration](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/auth/config.ts) and [job queues](https://github.com/dthompsonfl/eccb.app/blob/main/src/lib/jobs/queue.ts) | Member/admin processes, data access, roles and background workflows | Public active codebase; no claimed individual authorship of all modules |
| [Restaurant POS](https://github.com/dthompsonfl/pos) | [Implementation status](https://github.com/dthompsonfl/pos/blob/main/README.md) and [sync engine](https://github.com/dthompsonfl/pos/blob/main/data/src/main/java/com/enterprise/pos/data/sync/SyncEngine.kt) | Retail/restaurant workflows, checkout, local state, recovery and hardware integration boundaries | **Prototype**; production provider, persistence and hardware validation remains |
| [Desk Navbar Extended](https://github.com/dthompsonfl/desk_navbar_extended) | [Role-aware settings](https://github.com/dthompsonfl/desk_navbar_extended/blob/main/desk_navbar_extended/desk_navbar_extended/doctype/desk_navbar_extended_settings/desk_navbar_extended_settings.py) | Frappe operational UI configuration and role-scoped feature behavior | Modified existing Frappe app; source credits Gavin D'souza as original publisher. Deployment and adoption not independently validated |

## Developer tooling: source-backed examples

These tools show my interest in improving how engineering work is inspected and navigated. They are public code artifacts, not claims of independent production performance or benchmark superiority.

| Repository | Source to inspect | Engineering scope | Limit |
|---|---|---|---|
| [Hyperdrive Auditor](https://github.com/dthompsonfl/hyperdrive-audit) | [Auditor implementation](https://github.com/dthompsonfl/hyperdrive-audit/blob/main/src/audit/Auditor.mjs), [test/quality workflow](https://github.com/dthompsonfl/hyperdrive-audit/blob/main/.github/workflows/quality.yml) | JavaScript CLI for TypeScript/Next.js monorepo checks, boundary and dependency inspection, CI/SARIF reporting and guided review | No verified detection accuracy, adoption count or claim that current CI passed |
| [Apex Context Server](https://github.com/dthompsonfl/apex-context-server) | [Indexing engine](https://github.com/dthompsonfl/apex-context-server/blob/main/src/indexer.ts), [fixture tests](https://github.com/dthompsonfl/apex-context-server/blob/main/src/indexer.test.ts) | TypeScript MCP server with AST-aware indexing, symbol and route lookup, file context and persisted workspace memory | Source and test definitions inspected; no independent runtime or productivity benchmark |

## Cross-domain exploration and experimentation

These projects show the range of problems represented in the public account. They are **not ranked as proven production products**, and a listing does not assert independent personal authorship.

| Repository | Visible scope | Evidence boundary |
|---|---|---|
| [Clarinet Simulator](https://github.com/dthompsonfl/clarinet_simulator) | Python/Streamlit interface, [OpenWind adapter](https://github.com/dthompsonfl/clarinet_simulator/blob/main/src/simulation/physics.py), [optimization code](https://github.com/dthompsonfl/clarinet_simulator/blob/main/src/optimization/optimizer.py) and tests | Research prototype; the code was inspected, but simulation accuracy and tests were **not independently executed or benchmarked** |
| [Barrel Lab](https://github.com/dthompsonfl/barrelLab) | Streamlit clarinet-bore interface and [acoustics demonstration](https://github.com/dthompsonfl/barrelLab/blob/main/acoustics/acoustic_sim.py) | Exploratory UI; the visible impedance curve is simulated/placeholder mathematics, **not validated instrument physics** |
| [ProRoof](https://github.com/dthompsonfl/ProRoof) | Ruby/SketchUp [extension registration](https://github.com/dthompsonfl/ProRoof/blob/main/ProRoof.rb) and roof geometry modules | Public exploratory source; SketchUp runtime behavior, rights and authorship scope not independently certified |
| [Vantus File Indexer](https://github.com/dthompsonfl/vantus.file.indexer) | C#/XAML [application shell](https://github.com/dthompsonfl/vantus.file.indexer/blob/main/Vantus.App/MainWindow.xaml) and search UI | Public desktop-application source; no Windows build or user-deployment verification |
| [Frappe LLM](https://github.com/dthompsonfl/frappe-llm) | Training configurations, scripts, evaluation scaffolding and documentation for domain-focused model experiments | Public research/tooling artifact; **not** proof of a trained model surpassing a baseline or a production model deployment |
| [Shopify-to-ERP historical artifact](https://github.com/dthompsonfl/shopifysync1) | Minimal project README identifying Shopify/ERPNext direction | Historical evidence only; insufficient source to prove the broader integration implementation |

## How to interpret this index

1. **First-hand business stories** are documented separately and are not made stronger simply by attaching a GitHub link.
2. **Inspecting source** supports a statement about code that exists. It does not demonstrate production use, quantitative improvement or sole authorship.
3. **AI-assisted implementation** is included in the project history without pretending all resulting code was typed by hand.
4. **Public forks, copies and third-party templates** are intentionally excluded from the featured evidence. They can support learning or experimentation but should not be represented as original software.
5. **Private and client-sensitive systems** are not linked or exposed merely to increase the number of portfolio entries.

[Back to profile](../README.md) · [Capabilities](./PROFESSIONAL_CAPABILITIES.md) · [Detailed projects](./SELECTED_PROJECTS.md)

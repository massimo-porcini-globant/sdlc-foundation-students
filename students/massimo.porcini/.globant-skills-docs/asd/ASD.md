# Architecture Specification Document (ASD)

**Project:** Personal Finance Manager
**Version:** 1.0.0
**Date:** 2026-09-14
**Author:** Massimo Porcini
**Reviewer / Approver:** —
**Template Version:** 1.9.9

**Project Brief reference:** `docs/project-brief.md`

---

## About This Document

The ASD formalizes the architecture for this greenfield project, building on and deepening the technical decisions made during the presales process (Project Brief). It is the primary architectural reference for all delivery teams — engineering, cloud, DevOps, QE — and serves as the source of truth for downstream skills (implementation planning, coding guidance, security review).

This document is structured as a folder of linked Markdown files. Load individual section files based on the task at hand — do not load the full ASD unless necessary.

---

## Section Status

| # | Section | Status | Summary |
|---|---------|--------|---------|
| 1 | [Context](./01-context.md) | Done | Single-user local-first finance manager; REST API + Swagger UI; Java 21 / Spring Boot 3.4.0 / SQLite / Maven |
| 2 | [Functional Overview](./02-functional-overview.md) | Done | 5 capability areas from PB §2/§4; pure domain core + REST API + Swagger UI; CSV export; JSON summary endpoints as visualization (decided) |
| 3 | [Non-Functional Requirements](./03-nfr.md) | Done | Import 1k tx < 5 s; 360-rate schedule < 1 s; no uptime SLO (local); structured error responses; localhost-only |
| 4 | [Constraints](./04-constraints.md) | Done | 8 constraints incl. no-LLM-at-runtime, local-first, monovaluta BigDecimal; zero cost; no license checks |
| 5 | [Principles](./05-principles.md) | Done | 7 principles: pure domain core (lightweight hexagonal), Money value objects, fail-fast + ImportResult, API-first, YAGNI infra, versioned docs + ADR trigger, OOP discipline |
| 6 | [Software Architecture](./06-software-architecture.md) | Has open items | 6 PBCs (Import, Registry, Amortization, Categorization, Budgets, Reporting); modular monolith, lightweight hexagonal; single Maven module + ArchUnit; shared SQLite; data-flow + import sequence views; §6.3.2 per-PBC reference designs TBD |
| 7 | [Infrastructure Architecture](./07-infrastructure.md) | Done | Local runtime only: JVM + SQLite file + localhost; QA on dev machine; no CI/CD, ops, or frontend infra (N/A by design) |
| 8 | [Data Architecture](./08-data-architecture.md) | Done | N/A as analytics section (purely transactional); core ER model: Transaction, Category, Rule, Override, Budget, Alert, ImportBatch |
| 9 | [DevOps (CI/CD)](./09-devops.md) | Done | No pipeline (N/A); manual delivery workflow: mvn verify gates (unit + ArchUnit + integration), course branch/tag conventions; Flyway schema migrations (decided) |
| 10 | [Testing Principles](./10-testing.md) | Done | Pyramid: extensive unit (amortization/rounding), targeted integration (real SQLite + files), minimal E2E (1–2 flows), ArchUnit; NFR targets as tests; JaCoCo domain-only ≥ 90% (decided) |
| 11 | [Operation and Support](./11-operations-support.md) | Optional / N/A | Covered by §3.3 + §7.5: no monitoring/alerting (local single-user tool); backup = file copy; recovery = restart |
| 12 | [Digital Transformation Capabilities](./12-digital-transformation.md) | Optional / N/A | No runtime AI/cloud services (no-LLM-at-runtime constraint); the agent-assisted build process is documented in §1.3 |

**Legend (lifecycle order):**
- **Pending** — no content yet; awaits PB ingestion or user input
- **Pre-populated** — initial content from PB; awaits user confirmation and any gap-filling
- **In Progress** — currently being written
- **Has open items** — written but contains `[TO BE DEFINED]` markers (see Open Items below)
- **Done** — complete, no open items
- **Optional / N/A** — explicitly out of scope for this engagement

---

## Flagged Concerns

Issues identified during Project Brief validation that require attention before or during the ASD authoring process. Each concern is cross-referenced to the section it affects.

| ID | Concern | Type | Affects | Status |
|----|---------|------|---------|--------|
| CONCERN-001 | PB defines no NFRs (performance, volumes, error handling) — ASD §3 must define them | Missing NFRs | §3 | Resolved — NFR targets defined in §3 (import 1k tx < 5 s; 360-installment schedule < 1 s) |
| CONCERN-002 | Recorded stack included Testcontainers + SQLite; Testcontainers adds no value against SQLite | Tech stack gap | §1.3, §10 | Resolved — SQLite via JDBC (org.xerial) for both production and integration tests; Testcontainers dropped |
| CONCERN-003 | PB has no architectural-tactics section mapping NFRs → tactics | Incomplete tactic coverage | §3, §4.2 | Resolved — §3 pairs every NFR (3.1–3.4) with a Coverage block stating the tactics that address it (e.g., pure domain core + batched JDBC inserts for performance; SQLite WAL for reliability; localhost binding + parameterized SQL for security); §4.2 maps each constraint to its architectural response |
| CONCERN-004 | Currency handling unspecified in common transaction model | Assumption risk | §6, §8 | Resolved — monovaluta (EUR), amounts as BigDecimal + java.util.Currency from day one |

---

## Open Items

`[TO BE DEFINED]` items discovered across sections. Updated as sections are written.

| Section | Item | Priority |
|---------|------|----------|
| §6.3.2 | Per-PBC reference designs — decide which key PBCs (candidates: Transaction Import, Budgets & Alerts) warrant a documented reference design beyond §6.3.1 detail | Medium |

*1 open item — see table above. All other `[TBD]` defaults have been reviewed and either confirmed or revised.*

---

## How to Use This ASD

Load individual section files based on the task — do not load the full ASD at once.

| Task | Load these files |
|------|-----------------|
| Implementation planning | `06-software-architecture.md` + relevant section |
| Security review | `03-nfr.md` (§3.4) + `06-software-architecture.md` (§6.3.5) |
| Infrastructure work | `07-infrastructure.md` + `04-constraints.md` |
| DevOps / pipeline work | `09-devops.md` + `07-infrastructure.md` (§7.4) |
| Understanding requirements | `02-functional-overview.md` + `03-nfr.md` |

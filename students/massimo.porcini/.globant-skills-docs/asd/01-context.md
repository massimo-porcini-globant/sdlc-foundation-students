# 1 — Context

## 1.1 Business Context

The Personal Finance Manager is a local-first application for a **single, non-technical end user** managing their own money. People's financial lives are spread across disconnected sources — bank accounts, credit cards, digital wallets — each exporting data in its own format. The system consolidates those movements into one common model, computes amortization schedules for fixed-installment loans (French system), categorizes expenses deterministically, tracks budgets with threshold alerts, and produces reports and dashboards.

There is no multi-tenant or organizational requirement; multi-user authentication is explicitly out of scope. Success means the application is **runnable and verifiable** (REST API + Swagger UI), every financial calculation is **deterministic and reproducible**, and each capability is covered by tests.

The project is also the vehicle for an SDLC course: the build is agent-assisted (Globant skills harbor drives specification, planning, implementation, testing, and review), so the architecture must be legible to coding agents and skills.

**Reference:** No PRD exists — `docs/project-brief.md` (Project Brief) is the requirements source.

## 1.2 Related Projects / Initiatives

No related projects identified at this time.

The only external dependency is the shared `datasets/` folder containing sample export files (CSV/JSON) used as importer input. It is a file-based input, not a runtime integration.

## 1.3 Technical Context

### IT Strategy Context

Course constraints act as the governing "IT strategy" for this project:

- **Local-first** — no external service is required to run the application.
- **Deterministic core** — no LLM calls at runtime for financial calculations or categorization; all financial math and rules are plain code.
- **Agent-assisted delivery** — specification, planning, implementation, testing, and review are driven by CLI coding agents and the skills harbor; artifacts (ASD, plans) are versioned Markdown optimized for LLM context.
- **No real personal data** — sample data only; nothing sensitive is committed.

### Related Components

| Component | Type | Impact |
|-----------|------|--------|
| `datasets/` sample exports | File-based input (CSV/JSON) | Consumed by the transaction importer; formats drive normalization requirements |

No external systems are integrated; no components are deprecated or replaced.

### Development Toolchains

| Layer | Stack |
|-------|-------|
| Backend / API | Java 21, Spring Boot 3.4.0 (REST endpoints, springdoc-openapi for Swagger UI) |
| Persistence | SQLite via JDBC (`org.xerial:sqlite-jdbc`) — production and integration tests alike |
| Build | Maven |
| Testing | JUnit 5, Mockito, MockMvc / REST Assured |
| Presentation | Swagger UI (API-first; no separate frontend in scope) |

### Technical Document References

- [`docs/project-brief.md`](../../docs/project-brief.md) — Project Brief: goals, scope, solution sketch, constraints, open questions.
- [`datasets/`](../../../datasets/) — shared sample export files (course-provided).

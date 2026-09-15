# 4 — Constraints

## 4.1 Constraint Specification

**Reference:** [`docs/project-brief.md`](../../docs/project-brief.md) — §4 (Scope), §6 (Constraints and assumptions); user decisions (2026-09-14).

| Constraint | Type | Impact |
|-----------|------|--------|
| Stack is the student's choice (PB §6) — resolved to Java 21 + Spring Boot 3.4.0 + SQLite + Maven | Technology | Once chosen, the stack becomes a de facto constraint: all design must fit the JVM/Spring ecosystem |
| No LLM calls at runtime for financial calculations or categorization (PB §6) | Technology | Pure Java domain core (see `05-principles.md` — principle 1); LLMs participate only in the build process (coding agents, skills) — never in the running application |
| Local-first: no external service required to run the application (PB §6) | Technology | No cloud services, message brokers, external APIs, or remote databases at runtime; embedded SQLite only |
| No real personal data committed (PB §6) | Compliance | Sample/synthetic data only; no PII handling strategy needed (see `03-nfr.md` §3.4) |
| No production deployment (PB §4 out of scope) | Scope/Time | No environments beyond the developer machine; no CI/CD deployment targets (see `07-infrastructure.md`) |
| Course timeline — incremental delivery across sessions | Time | Features are built session by session; the architecture must accommodate incremental growth without rework (e.g., importer v1 in session 3, hardened in session 4) |
| Monovaluta EUR; amounts as `BigDecimal` + `java.util.Currency`, never `double` | Technology | No multi-currency conversion logic; but the money model must not preclude future multi-currency (type carries currency explicitly) |
| No license checks on dependencies | Compliance | Dependencies chosen freely on technical merit; no license-compatibility review required |

## 4.2 Coverage Strategy

How the architecture respects and works within each constraint:

- **Chosen stack (Java 21 / Spring Boot / SQLite / Maven)** → the architecture is a single Spring Boot application; no polyglot services, no framework proliferation. Spring's dependency injection keeps the pure domain core framework-agnostic (plain domain services, testable without Spring — see `05-principles.md` — principle 1).
- **No LLM at runtime** → the categorizer is a rule engine (ordered, deterministic rules); the amortization calculator is pure math. LLM involvement is confined to development-time agents, which never ship in the artifact.
- **Local-first** → embedded SQLite via JDBC instead of a DB server; Swagger UI served by the app itself; no feature depends on network access beyond localhost.
- **No real personal data** → security posture is minimal-by-design (§3.4); `datasets/` samples are the only inputs, and nothing sensitive ever enters version control.
- **No production deployment** → infrastructure section documents only the local runtime; no environment matrix, no DR, no deployment pipeline targets (see `07-infrastructure.md`).
- **Incremental course delivery** → the component model (section 6) isolates each capability (importer, amortization, categorizer, budgets, reports) behind stable interfaces so sessions can build them independently without cross-breaking changes.
- **Monovaluta with explicit currency type** → `Money`-style value object (`BigDecimal` amount + `Currency` field) satisfies today's EUR-only requirement while keeping the door open; no `double` anywhere in the money path.

## 4.3 Cost Constraints

### Specification
**Reference:** User decision (2026-09-14).

- **Infrastructure budget:** zero — the application runs entirely on the student's local machine. No cloud spend, no hosted services.
- **Cost model:** N/A (no CapEx/OpEx distinction applies; no paid services of any kind).

### Coverage
- All stack choices are free/open (JDK, Spring Boot, SQLite, Maven, JUnit) → no licensing or subscription costs.
- No cost-monitoring tactics required.

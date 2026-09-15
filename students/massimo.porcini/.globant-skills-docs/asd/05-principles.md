# 5 — Principles

The guiding design philosophy behind the architecture. Principles aren't rules — they're the beliefs that explain why decisions were made. When two options are both technically viable, these break the tie.

| # | Principle | Rationale |
|---|-----------|-----------|
| 1 | **Pure domain core, adapters at the edges** — domain logic resides in a pure Java core, free from framework dependencies; Spring, JDBC, and REST are adapters connected to well-defined boundaries/interfaces (importer parsers, report exporters, persistence). Lightweight hexagonal style; no dedicated ports/adapters packages until complexity justifies it. | Enables exhaustive unit testing of the domain without Spring, keeps financial logic reproducible, and lets each capability evolve independently across course sessions. Merges the former "deterministic core" and "SOLID/DRY" principles: standard OOP discipline (SOLID, DRY) applies inside the core as a consequence of this principle, not as a separate commitment. |
| 2 | **Money is never a primitive** — amounts are always `BigDecimal` + `Currency` value objects; never `double` for monetary values. | Prevents floating-point financial bugs; keeps the model multi-currency-ready while today operating EUR-only (see `04-constraints.md`). |
| 3 | **Fail fast, report clearly** — invalid input raises explicit exceptions; malformed data is reported per-row using a typed result mechanism (e.g., `ImportResult` distinguishing accepted rows from rejected ones and flagged possible duplicates, with rejection reason); data is never silently corrected or discarded without a trace. | Financial data integrity demands explicitness. The typed mechanism is a concrete design obligation for the importer, not a generic intention (see `02-functional-overview.md` and section 6 component design). |
| 4 | **API-first** — every feature is exposed via REST before any UI consideration; Swagger UI serves as the reference consumer. | Keeps presentation thin, the core testable via MockMvc/REST Assured, and delivery verifiable without building a frontend. |
| 5 | **Local-first simplicity (YAGNI for infrastructure)** — no distributed-systems mechanisms (queues, caches, service discovery) unless required by a stated NFR. | The NFR set (`03-nfr.md`) is modest and local; infrastructure complexity would be unjustified and would violate the local-first constraint. |
| 6 | **Versioned, agent-legible documentation** — project documentation (ASD, plans) uses versioned Markdown; an **ADR is added whenever an architectural principle or choice changes**. | Coding agents are first-class team members; versioned Markdown is their native context. The ADR trigger makes the commitment verifiable rather than a generic promise to "keep docs up to date". |
| 7 | **Standard OOP discipline** — conventional object-oriented design for the domain model (meaningful names, encapsulation, value objects). | Baseline code quality; subsumed in practice by principle 1's hexagonal core but stated explicitly as the modeling style. |

**Reference frameworks considered:** Hexagonal Architecture (lightweight adoption, per principle 1), TOGAF Architecture Principles, YAGNI.

**Operational follow-ups agreed:**
- Principle 3 → the `ImportResult`-style typed mechanism must be designed as part of the importer component (section 6 / implementation planning).
- Principle 6 → ADRs live under `docs/adrs/` (per `docs/README.md`); one is written whenever a principle or architectural choice changes.

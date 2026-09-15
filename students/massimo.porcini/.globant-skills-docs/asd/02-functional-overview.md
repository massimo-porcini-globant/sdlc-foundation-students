# 2 — Functional Overview

## 2.1 Functional Requirements References

**Source:** [`docs/project-brief.md`](../../docs/project-brief.md) — §2 (Goals and success criteria), §4 (Scope).

| Requirement Area | Reference | Notes |
|-----------------|-----------|-------|
| Amortization schedule | PB §2, §4 | Fixed-installment loan (French system); TAN, half-up rounding per installment, remainder on last installment — see §2.2 |
| Transaction importer | PB §4 | Parses and normalizes **at least 3** export formats (CSV and JSON) into the common transaction model; handles differing date formats, decimal separators, sign conventions, duplicates, and malformed rows (three-state per-row result: accepted / rejected / flagged as possible duplicate — never silently dropped) |
| Expense categorizer | PB §4 | Rule-based and deterministic (no LLM at runtime); user can review and correct categories; corrections persist as learned overrides |
| Budgets & alerts | PB §4 | Budgets per category with statistical thresholds, a monthly digest, and threshold-based alerts |
| Reports & dashboards | PB §4 | At least one visualization and one export format (CSV) |

**Out of scope (explicitly excluded):**
- Multi-user accounts, authentication, and authorization.
- Real bank integrations or live data feeds (sample exports in `datasets/` only).
- Payments, transfers, or any write-back to real financial institutions.
- Mobile apps or production deployment.
- Custom web UI — Swagger UI is the presentation layer; category review/correction happens via REST endpoints.

## 2.2 Coverage Strategy

A local-first Spring Boot application organized around a **pure domain core** (see `05-principles.md` — principle 1): all financial math (amortization), normalization rules (importer), and categorization rules are plain Java domain services — no LLM at runtime. The core is exposed through a REST API documented and exercised via Swagger UI, which doubles as the presentation layer.

The data flows in one direction: raw exports are imported and normalized into a **common transaction model** (amounts as `BigDecimal` + `java.util.Currency`, EUR monovaluta), persisted locally in SQLite, then consumed by the categorizer, the budget engine, and the reporting layer.

**Key architectural choices enabling functional requirements:**
- Pure domain core (principle 1, `05-principles.md`) → enables verifiable, reproducible financial calculations (PB success criterion) and exhaustive unit testing of rounding edge cases.
- Common transaction model + pluggable format parsers → enables consolidation of heterogeneous sources (≥3 formats) without corrupting data.
- SQLite via JDBC, local-first → zero external dependencies; the app runs anywhere Java runs.
- REST API + Swagger UI → runnable-and-verifiable delivery without building a custom frontend; review/correction flows exposed as endpoints.
- CSV export endpoints → satisfies the reporting export requirement with minimal surface area.

**Requirements deferred to PBC-level design (section 6):**
- Exact shape of the common transaction model (fields, types) — designed in the Data Architecture and component design.
- Rate convention (nominal vs effective) and rounding policy for amortization — **decided**: nominal annual rate (TAN) converted to the installment period; half-up rounding to 2 decimal places per individual installment; accumulated rounding difference applied to the final installment.
- Statistical threshold definition for budgets — **decided**: moving average over N months (default 6) with threshold at k·σ (default k=2), minimum 3 months of history; formal definition in §6.3.1 of `06-software-architecture.md`.
- Visualization format — **decided**: aggregated JSON summary endpoints (monthly balance, spending by category, trends) rendered via Swagger UI; no dedicated visualization/charting layer — REST-first with Swagger UI as the reference consumer makes a charting layer unjustified for a single-user app.

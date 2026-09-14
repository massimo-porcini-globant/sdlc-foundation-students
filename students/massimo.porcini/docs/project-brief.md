# Project Brief — Personal Finance Manager

> **Pre-built brief.** Unlike the other files in `templates/` (blank templates you fill in yourself), this one already comes resolved for the course domain. In session 0 you copy it into your workspace; in session 1 you read it end to end and feed it to the `asd-create` skill to produce your Architecture Specification Document (ASD).
>
> ```bash
> cp templates/project-brief.md students/<your.username>/docs/project-brief.md
> ```
>
> It is intentionally a **presales-style** document: it states goals, scope, and a rough solution sketch, but it deliberately leaves stack, non-functional requirements, and detailed design decisions open — those are what `asd-create` will interrogate you about.

---

## 1. Context and problem

People manage their money across several disconnected sources — bank accounts, credit cards, and digital wallets — each exporting data in its own format. There is no single place that consolidates those movements, categorizes spending, projects loan payments, and warns before a budget is blown. The goal of this project is a **personal finance manager**: a small but real application that a single person can run to understand and control their finances.

The project is the vehicle for the course. The domain is deliberately modest so the focus stays on **how** you build it with a coding agent and the skills harbor, not on financial complexity.

## 2. Goals and success criteria

- Consolidate transactions from multiple heterogeneous sources into one common model.
- Compute an **amortization schedule** for a fixed-installment loan (French system).
- **Categorize** expenses deterministically and let the user correct categories.
- Track **budgets** per category and raise alerts when thresholds are approached or exceeded.
- Produce **reports and dashboards** (monthly balance, spending by category, trends).

Success = the application is **runnable and verifiable** (via CLI, API, or web — the student decides), every financial calculation is deterministic and reproducible, and each capability is covered by tests.

## 3. Target user

A single, non-technical end user managing their own money. There is no multi-tenant or organizational requirement. (Multi-user authentication is **out of scope** for this course.)

## 4. Scope

**In scope** (built incrementally across the course):

- Amortization table for a fixed-installment loan, with documented rate convention and rounding policy.
- Transaction importer that parses and normalizes **at least three** different export formats (CSV and JSON) into the common model, handling different date formats, decimal separators, sign conventions, duplicates, and malformed rows.
- Rule-based expense categorizer with a review screen and learned user overrides.
- Budgets with statistical thresholds, a monthly digest, and threshold-based alerts.
- Reports and dashboards with at least one visualization and one export format.

**Out of scope:**

- Multi-user accounts, authentication, and authorization.
- Real bank integrations or live data feeds (sample exports are provided in `datasets/`).
- Payments, transfers, or any write-back to real financial institutions.
- Mobile apps or production deployment.

## 5. Solution sketch

A local-first application with a **deterministic core** (all financial math and rules are plain code — no LLM at runtime) and a thin presentation layer chosen by the student. Raw exports are imported and normalized into a common transaction model, persisted locally, then consumed by the categorizer, the budget engine, and the reporting layer. The build itself is agent-assisted: skills and coding agents from the Globant harbor drive specification, planning, implementation, testing, and review.

## 6. Constraints and assumptions

- **Stack is the student's choice** (language, framework, storage) — the assignments describe behavior, not implementation.
- No LLM calls at runtime for financial calculations or categorization; those must be deterministic and testable.
- Local-first: no external service is required to run the application.
- Sample data lives in the shared `datasets/` folder; no real personal data is committed.

## 7. Open questions for `asd-create` to resolve

These are intentionally unresolved here; expect the skill to interrogate you about them:

- Target stack and persistence mechanism.
- Exact shape of the common transaction model (fields, types, currency handling).
- Rate convention (nominal vs effective) and rounding policy for the amortization schedule.
- Non-functional requirements: performance targets, data volumes, error handling strategy.
- Testing and quality strategy, and how reports will be rendered and exported.

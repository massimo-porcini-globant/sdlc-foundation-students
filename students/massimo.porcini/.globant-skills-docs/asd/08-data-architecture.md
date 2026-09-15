# 8 — Data Architecture

**Scope:** Not applicable as an analytics/data-platform section — this is a purely transactional system with no data lake, warehouse, BI platform, or ML consumption (local-first constraint, `04-constraints.md`; PB §4). Operational data ownership and flow are covered in `06-software-architecture.md` §6.3.3. This section documents the core entity model for downstream planning, then explicitly marks the analytics concerns out of scope.

## Core Entity Model

> *Auto-generated from PB content — confirmed with the user (2026-09-14).*

```mermaid
erDiagram
    TRANSACTION {
        integer id PK
        text source
        text external_id
        text booking_date
        real_amount amount
        text currency "always EUR (monovaluta)"
        text direction "debit | credit"
        text raw_description
        text normalized_description "produced by importer normalization rules (trim, case, whitespace, date/decimal canonicalization — §2.1); owner: Transaction Import"
        integer category_id FK "nullable until categorized"
        text dedup_key "unique when external_id present; else nullable fingerprint key — see dedup rules below"
    }

    CATEGORY {
        integer id PK
        text name "unique"
        text description
    }

    CATEGORY_RULE {
        integer id PK
        integer priority "ordered; first match wins"
        text pattern "case-insensitive substring match on normalized_description"
        integer category_id FK
    }

    CATEGORY_OVERRIDE {
        integer id PK
        integer transaction_id FK "unique"
        integer category_id FK
        text corrected_at
    }

    BUDGET {
        integer id PK
        integer category_id FK
        text period "monthly"
        real_amount limit_amount "nullable — optional self-imposed target"
        text threshold_policy "moving avg N=6 + k·σ (k=2), min 3 months history — see §6.3.1 algorithm"
    }

    IMPORT_BATCH {
        integer id PK
        text source_format
        text file_name
        text imported_at
        integer accepted_rows
        integer rejected_rows
        integer possible_duplicate_rows
    }

    ALERT {
        integer id PK
        integer category_id FK "category the alert is about"
        integer budget_id FK "nullable — set only for limit_amount alerts"
        text alert_type "statistical_threshold | limit_amount_exceeded"
        text period "reference month, e.g. 2026-09"
        real_amount triggering_value "month spending that fired the alert"
        real_amount threshold_value "threshold it exceeded (statistical or limit_amount)"
        text generated_at "generation timestamp"
        text status "unread | read"
    }

    CATEGORY ||--o{ TRANSACTION : classifies
    CATEGORY ||--o{ CATEGORY_RULE : "target of"
    CATEGORY ||--o{ BUDGET : "budgeted"
    CATEGORY ||--o{ ALERT : "generates"
    BUDGET ||--o{ ALERT : "triggers"
    TRANSACTION ||--o| CATEGORY_OVERRIDE : "corrected by"
```

**Modeling notes:**
- `amount` is `BigDecimal` + `Currency` in the domain (principle 2, `05-principles.md`); the SQLite column stores the decimal value with the currency fixed to EUR (monovaluta, `04-constraints.md`).
- `dedup_key` enforces Registry deduplication (§6.3.4): re-importing overlapping date ranges reports duplicates instead of double-counting. **Two cases:**
  - **`external_id` available (primary, reliable):** `dedup_key = source + external_id + booking_date + amount`. A match with an existing transaction is a **certain duplicate** — the row is reported in `ImportResult` as a duplicate and not imported.
  - **`external_id` NOT available (fallback):** no silent auto-dedup (it would violate principle 3 — data is never discarded without a trace). Instead a **composite fingerprint** is computed — `booking_date + amount + normalized_description + source` — and if it matches an existing transaction, the row is **not discarded and not imported automatically**: it is flagged in `ImportResult` as *"possible duplicate of transaction #X"*, leaving the final decision to explicit review (manual confirmation), never an automatic rejection.
- `CATEGORY_OVERRIDE` takes precedence over `CATEGORY_RULE` (first-match-wins engine, §6.3.4).
- **`ALERT` — persisted record of a fired alert** (owner: Budgets & Alerts module; persistence declared in §6.3.1 and shown in the Data Flow View). One row per alert actually fired:
  - `alert_type` distinguishes the two mechanisms already formalized: `statistical_threshold` (anomaly vs. own history, §6.3.1 algorithm) and `limit_amount_exceeded` (self-imposed target exceeded). The two can coexist for the same month/category — they generate **separate ALERT rows**.
  - `budget_id` is nullable: set only for `limit_amount_exceeded` alerts (which require a budget); statistical alerts reference only the category.
  - `threshold_value` records the threshold at generation time (moving average + k·σ, or the `limit_amount`) so historical alerts remain interpretable even after the threshold changes.
  - `status` (unread/read) supports `GET /api/alerts` and the monthly digest; alerts are never deleted — history is part of the digest's value.
- **`limit_amount` vs statistical threshold — two independent, complementary alert mechanisms:**
  - `limit_amount` is **optional (nullable)** per category — not all categories need one. If set, it triggers a dedicated **"budget exceeded"** alert when the current month's spending in that category exceeds it.
  - The **statistical threshold** (moving average + k·σ, §6.3.1) operates in parallel and automatically — once at least 3 months of history exist — regardless of whether `limit_amount` is set.
  - The two alerts **can coexist** for the same month/category without prioritization or reconciliation: they are signals of a different nature (self-imposed target vs. anomaly relative to one's own history) and both are displayed.
- `IMPORT_BATCH` records per-import summaries (accepted, rejected, and possible-duplicate rows) supporting the `ImportResult` reporting obligation (principle 3).

## Analytics / Data Platform

**Analytics / BI platform:** none.
**Data ingestion pipeline:** none — no CDC, no ETL; the SQLite file is the single data store.
**Data consumers:** the single user, via REST reporting endpoints (§6.3.1).
**Real-time analytics:** not required.
**ML / AI integration:** none at runtime (no-LLM-at-runtime constraint, `04-constraints.md`).
**Data governance:** not applicable — sample/synthetic data only (§3.4).
**Data retention and archiving:** none — the user manages the DB file; backup is a manual file copy (§3.3).

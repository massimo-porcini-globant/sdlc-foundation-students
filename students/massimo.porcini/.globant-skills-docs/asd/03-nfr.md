# 3 — Non-Functional Requirements (NFRs)

Each NFR pairs a **Specification** (measurable requirement) with **Coverage** (the architectural tactics that address it). A requirement without coverage is a wish; coverage without a requirement is ungrounded design.

## 3.1 Performance and Scalability

### Specification
**Reference:** User decision (2026-09-14); PB §7 left this open.

- **Import throughput:** importing 1,000 transactions completes in **< 5 s** (including parsing, normalization, and persistence).
- **Amortization computation:** a full 360-installment schedule is computed in **< 1,000 ms**.
- **Expected data volume:** **decided** — ~10,000 transactions/year, single user. This volume is the underlying assumption of the import NFR above (1,000 transactions < 5 s ≈ one month of activity at the expected rate); well within SQLite's operating envelope.
- **Load profile:** single user, local machine. No concurrent-user target; no peak-load scenarios.
- **Scalability model:** vertical only (JVM on a laptop). Horizontal scaling is explicitly out of scope — the app is local-first and single-user (see `01-context.md` §1.1).

### Coverage
- Pure domain core (principle 1, `05-principles.md`; no I/O in the calculation path) → amortization target is bounded by CPU only; 360 installments is trivially fast in plain Java.
- Batched inserts via JDBC (single transaction per import) → import target dominated by SQLite write throughput, which comfortably exceeds 200 rows/s.
- No caching, sharding, or async processing needed at these volumes — added complexity would be unjustified.

## 3.2 Availability and Reliability

### Specification
**Reference:** User decision (2026-09-14, defaults accepted).

- **Uptime SLO:** none — local application started on demand; availability is the user's responsibility, not the system's.
- **RPO:** no data loss on graceful shutdown; all committed transactions are durably persisted in the SQLite file. The SQLite database file is the unit of backup.
- **RTO:** application restart — seconds.
- **Maintenance window:** N/A (no deployment cadence; the app is restarted at will).

### Coverage
- SQLite journal mode (WAL) → crash-safe commits; a killed process loses nothing already acknowledged.
- Single-file storage → backup/restore is a file copy; no replication or failover machinery to operate.

## 3.3 Operational Requirements

### Specification
**Reference:** User decision (2026-09-14, defaults accepted).

- **Monitoring/alerting:** none — out of scope for a local single-user tool (no metrics stack, no alert routing, no support SLA).
- **Logging:** standard Spring Boot logging (SLF4J/Logback) to console and file; import runs log per-format summaries (rows parsed, accepted, rejected).
- **Audit/compliance logging:** none required (sample data only; see §3.4).
- **Backup policy:** manual copy of the SQLite file; no scheduled backup.

### Coverage
- Spring Boot defaults + a logback config → logging requirement met with zero custom infrastructure.
- Importer rejection report (row number + reason) → operational diagnosability of bad data without a monitoring stack.

## 3.4 Security and Privacy

### Specification
**Reference:** PB §4 (out of scope: multi-user/auth), PB §6 (no real personal data); user decision (2026-09-14, defaults accepted).

- **Regulatory requirements:** none — no GDPR/PCI/HIPAA scope; the app processes sample/synthetic data and never real personal financial data (PB §6).
- **Data classification:** financial-shaped but synthetic; treated as low sensitivity.
- **Auth model:** none — single user on localhost; authentication and authorization are explicitly out of scope (PB §4).
- **Security standards:** none mandated.

### Coverage
- **Network exposure:** server binds to `localhost` by default → the API is not reachable from other machines.
- **Input validation:** all REST endpoints validate input (bean validation) and return structured errors; importer rows are validated per-row — malformed rows are rejected and reported, never crash the app (see Error-Handling Strategy below).
- **SQL injection:** all persistence via parameterized JDBC statements (no string-concatenated SQL).
- **Secrets management:** none needed — no external services, no credentials (local-first constraint, see `04-constraints.md`).
- **Encryption:** not required at rest (local file under user control); no TLS in scope (localhost only).

## Error-Handling Strategy

Cross-cutting NFR agreed with the user (resolves the PB §7 open point):

- **REST API:** all errors return a consistent structured JSON body — `timestamp`, `status`, `error`, `message`, `details` — via a global exception handler (`@ControllerAdvice`). No stack traces leak to clients.
- **Importer:** malformed rows are **collected and reported per-row** (row number + reason), never silently dropped and never fatal to the batch. The import result distinguishes three per-row outcomes: accepted, rejected, and possible duplicate (fingerprint match without `external_id` — flagged for review, never auto-rejected).
- **Domain layer:** financial calculations fail fast with explicit exceptions on invalid input (e.g., non-positive principal, inconsistent rate) — invalid states are never silently coerced.

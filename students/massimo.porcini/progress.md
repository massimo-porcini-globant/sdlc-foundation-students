# Course progress

**Student:** <Massimo Porcini>
**Globant username:** massimo.porcini
**CLI assistant:** <CODA>
**Model:** <GLM-5.3-Flash>
**Chosen stack:** <Java 21 + SpringBoot 3.4.0 + Spring Boot Testing with JUnit 5, Mockito & Testcontainers + SQLLite>
**Start date:** <2026-09-14>

---

## Session 0 — Setup and orientation

- [x] CLI agent (CODA / Kilo / Cline) installed and working from `students/<your.username>/`
- [x] The 5 harbor skills installed where your agent expects them and discoverable
- [x] Personal folder created from `students/_template/`, branch `student/<your.username>` pushed
- [x] Pre-built Project Brief copied into `docs/project-brief.md`
- [x] `progress.md` header customized (name, username, assistant, model, stack, start date)
- [x] Project memory (AGENTS.md or equivalent) includes the session-closing convention
- [x] Tag `<your.username>/s0` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 1 — SDD: foundations and architecture

- [ ] Can explain SDD and why it matters when an agent does the implementation
- [ ] Reviewed the SDD framework landscape (Spec Kit, Kiro, Tessl)
- [ ] `asd-create` run against your Project Brief, producing a complete ASD
- [ ] ASD reviewed section by section; every `[TBD]` resolved or knowingly deferred
- [ ] ASD committed and versioned (the `.globant-skills-docs/asd/` folder with its `ASD.md` index) with an initial version marker
- [ ] Tag `<your.username>/s1` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 2 — Planning and implementation (amortization table)

- [ ] `implementation-plan` run anchored to the ASD; plan versioned separately from the code
- [ ] Interrogation covers zero-rate, rate-conversion, and rounding-residual, each resolved
- [ ] Feature implemented with `coding`; diff reviewed
- [ ] Tests generated with `testing-implementation`, each labeled blackbox/whitebox
- [ ] All tests pass; at least one installment validated by hand against an external source
- [ ] Rate convention and rounding policy documented in a README "Conventions" section
- [ ] Tag `<your.username>/s2` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 3 — Write your own skill (transaction importer v1)

- [ ] Skill teardown of `g-e-coding` produced (5 points) in `docs/skill-teardown-g-e-coding.md`
- [ ] Architecture notes updated with skill/model decisions
- [ ] At least one versioned skill of your own (`analyze-csv-format`) in your agent's folder
- [ ] Skill produces structured output; proposes a mapping for 1-2 CSV formats from `datasets/`
- [ ] Minimal importer applies the discovered mapping into the common model
- [ ] Own skill documented; commit history shows at least one iteration/refinement
- [ ] Tag `<your.username>/s3` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 4 — Adopt and modify harbor skills (importer to real quality)

- [ ] `implementation-plan` drove the increment, anchored to the ASD
- [ ] `coding` implemented; importer parses/normalizes/persists at least 3 formats from `datasets/`
- [ ] Normalization handles decimal separators, date formats, and duplicates without corruption
- [ ] Malformed rows rejected/reported (not dropped); tests per format + robustness cases pass
- [ ] A local copy of a harbor skill modified for a documented purpose, with a concrete before/after
- [ ] Build/adopt/adapt write-up completed; ASD updated
- [ ] Tag `<your.username>/s4` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 5 — Build your coding agents (expense categorizer)

- [ ] Four CLI agents defined (tech-lead, frontend dev, backend dev, UI/UX designer), reusable
- [ ] Each agent has a spec/contract (role, allowed tools, I/O, success criteria, fallback)
- [ ] backend-dev implemented the categorizer; tech-lead reviewed it
- [ ] UI/UX designer + frontend-dev built the categorization review screen
- [ ] Categorizer deterministic (no LLM at runtime); rules catalog with a defined evaluation order
- [ ] User corrections persist as learned overrides; at least one ADR documented
- [ ] Tag `<your.username>/s5` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 6 — Coordinate agents: automate the flow (budgets with alerts)

- [ ] Your ASD documents the chosen coordination pattern + flow decomposition
- [ ] At least 3 of the 4 session-5 agents chained into one automated flow with documented contracts
- [ ] Flow automated via assistant primitives (not manual copy-paste between agents)
- [ ] Budgets feature deterministic: calculations, statistical thresholds, templates, no runtime LLM
- [ ] Monthly digest + threshold-based alerts working; at least one agent-failure case handled
- [ ] ADR on the coordination pattern; cost-of-automating note documented
- [ ] Tag `<your.username>/s6` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 7 — Quality: the QA-dev feedback loop (reports and dashboards)

- [ ] `docs/standards.md` derived from `coding`; linter/formatter + a blocking local hook in place
- [ ] QA agent spec exists (role, read-only tools, I/O, success criteria, fallback, comment cap)
- [ ] QA integrated into the session-6 flow as a bidirectional loop (findings → fix/flag → resolve)
- [ ] Tests closed with `testing-implementation` (blackbox/whitebox classified)
- [ ] Gap report from `identify-technical-debt`; at least 3 items refactored
- [ ] Reports feature works (≥1 visualization, ≥1 export format); `docs/reviewer-log.md` with ≥5 entries
- [ ] Tag `<your.username>/s7` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Session 8 — Course retrospective

- [ ] `retro.md` created from `templates/retro-template.md` and filled in (≤ 1 h)
- [ ] Session 8 marked in this `progress.md` (ideally all 9 sessions complete)
- [ ] Final commit `[Agent/Model]: session 8 - ...` pushed to the repo
- [ ] Tag `<your.username>/s8` created and pushed (`git push --follow-tags`)

**Notes:**

---

## Overall reflection

<Free space for your general takeaways from the course, key decisions you made, resources you discovered, etc.>

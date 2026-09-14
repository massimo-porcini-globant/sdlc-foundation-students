# My workspace

<Replace these two lines with who you are and what you are building. Example: "I am Jose Arevalo (`jose.arevalo`) and here I build my personal finance manager during Globant's Agentic SDLC Foundation course.">

---

## Organization of this folder

```
students/<your.username>/
├── README.md         # this file
├── progress.md       # per-session progress checkboxes (do not delete)
├── .gitignore        # keeps the confidential harbor skills out of git
├── docs/             # project docs: project-brief, ADRs, teardowns, quality artifacts...
├── .globant-skills-docs/  # ASD + implementation plans, written by the harbor skills (sessions 1-2)
├── src/              # your code (free structure inside)
└── .coda/
    ├── agents/       # agent definitions you create
    ├── skills/       # custom skill definitions you create
    └── plans/        # implementation plans (if your tool stores them here)
```

### `progress.md`

Critical file. Check each box as you complete the items. The course tracking agent reads it to report the group's status. Do not delete it or change its header structure.

### `docs/`

Where your project documentation lives. Across the course you will produce, among others: `docs/project-brief.md` (copied from `templates/`), ADRs, and skill teardowns. Your ASD and your implementation plans are written by the harbor skills to `.globant-skills-docs/` (session 1 onwards) — version that folder too.

### `.coda/`

Definitions of the agents and skills you create during the course. This template uses CODA's layout as the example. **If you use a different assistant, organize these under the path your tool expects** and see the course material for the tool-specific layout:

- **CODA:** `.coda/agents/`, `.coda/skills/`, `.coda/plans/`
- **Kilo Code:** `.kilocode/` (`custom_modes.yaml`, `rules/`, `mcp.json`, ...)
- **Cline:** `.clinerules/` (`memory-bank/`, `rules/`, `workflows/`, ...)

Version these definitions in the repo — they are part of your assessable work.

### `src/`

Your code. The internal structure is up to you — adapt it to your stack.

---

## Reminders

- Work on your `student/<your.username>` branch, inside `students/<your.username>/`. You never push to `main`.
- When closing each session: commit `[Agent/Model]: session N - Description`, push the branch, and tag `<your.username>/sN` (`git push --follow-tags`).
- Keep `progress.md` up to date — the tracking agent depends on that file.

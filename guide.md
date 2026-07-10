# LLM Wiki — the pattern

The memory system a coding agent uses to accumulate knowledge across sessions.

Inspired by [LLM-as-wiki-curator, Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

---

## Core idea

Instead of passing raw context to the agent every session, keep an **LLM-curated wiki** — structured markdown the agent reads, updates, and consults. Knowledge accumulates instead of being lost between sessions.

Three layers:
1. **Raw sources** — code, decisions, conversations (immutable)
2. **Wiki** — LLM-synthesized pages, structured and cross-linked (current state)
3. **`SCHEMA.md`** — the map: what exists, what to load, when

## Structure

`SCHEMA.md` has three required sections: `## Pages` (page → description), `## By topic` (topic → page, for fast lookup without reading the whole file), and `## Operations` (ingest/query/lint).

```
<project>/
├── wiki/
│   ├── SCHEMA.md        ← read first every session
│   ├── stack.md         ← tech, versions, env vars
│   ├── conventions.md   ← critical rules & gotchas
│   ├── database.md      ← schema, tables, access rules
│   ├── api.md           ← routes, endpoints, flows
│   ├── business.md      ← model, phase history
│   └── …                ← per-project pages
├── AGENTS.md            ← lean session bootstrapper
├── log.md              ← append-only session log
└── plans/
    ├── open/  ├── closed/  └── _INDEX.md
```

## Operations

### Ingest
When work ships (the close-out protocol), the agent:
1. Reads the relevant wiki pages.
2. **Edits the matching section to reflect current state** — corrects/replaces what changed, deletes what's obsolete. Does **not** stack a new paragraph next to the old one.
3. Keeps the plan in `## Related Plans` as a **raw link, no prose** — the delivery summary already lives in `log.md`.
4. Updates the `Updated:` field **with the date only** — no summary of what changed.

The wiki describes the **current state**, not the historical sequence of how you got there. History is the job of `log.md` and `_INDEX.md`. A page that accumulates a long `Updated:` block, `## Related Plans` with per-plan prose, or `(TASK-NNN)` tags stuck on every rule has become a duplicated changelog — out of pattern, needs distillation.

### Query
When context is needed mid-session:
1. Read `wiki/SCHEMA.md` for the map.
2. Load the specific pages on demand.
3. Never load everything at once — only what's needed.

### Query-back
Valuable syntheses that surface ad-hoc during a session — diagnoses, approach comparisons, answers that clarify something about the project — should be **ingested into the wiki** instead of being lost in the conversation. Can be a standalone ingest: "Ingest [concept/synthesis]".

### Lint
Periodic review with parallel agents (one per page). Checks: stale content, broken links, missing plan backlinks, **and signs of duplicated changelog** — long `Updated:` block, `## Related Plans` with per-plan prose, high density of `(TASK-NNN)` tags in the body, dead schema/rules documented at length instead of removed. Proposes edits, waits for approval before writing. See [`health-check.md`](health-check.md).

## Plans vs Specs

- **`plans/`** records the **how** — any structured planning, any phase: content strategy, layout exploration, code implementation. A full-cycle project (content → design → dev) uses a single continuous sequence of plans (PLAN-001 content, PLAN-002 layout, PLAN-003 dev…), not separate folders per phase.
- **`specs/`** records the **what** — final artifacts handed to the client (a spec doc, a brand-voice doc, wireframes).

## Principles

- **One source of truth per fact** — the wiki is the repository; the bootstrapper (`AGENTS.md`/`CLAUDE.md`) only bootstraps.
- **Append-only in the log, prunable in the wiki** — `log.md` never rewrites, only adds; wiki pages do the opposite: describe current state and **replace/remove** what's obsolete on every ingest. Per-plan history lives in the log, not the wiki.
- **One direction in the log** — every new entry goes **at the end** (chronological, oldest on top). Never insert at the top, never reorder old entries.
- **Explicit links** — always path-qualified cross-links; never bare links that resolve ambiguously in the graph.
- **Lean by default** — a wiki page exists to be read whole in one session. If it grows to the point of burning context without adding durable knowledge (plan-by-plan changelog, provenance tags on every rule), that's a signal of pending pruning, not a "healthy wiki".

---

← [README](README.md)

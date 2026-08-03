# LLM Wiki — a self-maintaining knowledge base for coding agents

A practical operating model for the **LLM-as-wiki-curator** pattern: instead of feeding a coding agent raw context every session, you keep a **curated wiki** of markdown pages the agent reads, updates, and prunes. Knowledge accumulates instead of evaporating between sessions.

Inspired by [Andrej Karpathy's LLM-as-wiki-curator gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). This repo adds the part that's hard to get right in practice: **keeping the wiki lean over time** so it doesn't rot into a changelog.

> Reference implementation: [Claude Code](https://claude.com/claude-code) + Obsidian (via an MCP vault server). The pattern is agent- and store-agnostic — swap in your own.

---

## The idea

Three layers:

1. **Raw sources** — code, decisions, conversations. Immutable, authoritative.
2. **Wiki** — LLM-synthesized pages with structure and cross-links. Describes the **current state** of the system.
3. **`SCHEMA.md`** — the map of the wiki: what exists, what to load, and when. Read first, every session.

The agent **queries** the wiki on demand (never loads everything), and **ingests** new knowledge when work ships. A short `log.md` keeps the append-only history; the wiki itself stays current-state.

## Why most LLM wikis rot (and the fix)

Left alone, an LLM wiki degrades into a changelog. Each "ingest" *appends* — a new `Updated:` line, another `## Related Plans` entry, a `(TASK-123)` provenance tag on every rule — until the page is mostly history and burns context without adding durable knowledge.

The fix is a **two-sided loop**:

- **Proactive (at ingest):** the close-out protocol *distills* instead of appends — it corrects/replaces the current-state text, keeps `Related Plans` as raw links (capped), forbids provenance tags and per-task prose in the body, and runs a final self-check before finishing.
- **Reactive (periodic):** a lightweight health check flags pages showing changelog bloat (oversized backlink lists, more than one `Updated:` line or backlink section, dense provenance tags) so drift gets caught, not accumulated.

The wiki describes **the current state**; history lives in the `log` and the plan index. A page that reads like a diff is a signal to prune, not a healthy wiki.

## What's in here

| File | What it is |
|---|---|
| [`guide.md`](guide.md) | The pattern reference — layers, structure, operations (ingest/query/lint), principles |
| [`protocols/new-plan.md`](protocols/new-plan.md) | Open a unit of work: create the plan file, update the index + log, branch git |
| [`protocols/close-plan.md`](protocols/close-plan.md) | Ship it: distill into the wiki (the anti-bloat rules live here), archive, log |
| [`protocols/wiki-setup.md`](protocols/wiki-setup.md) | Bootstrap a fresh project's wiki (SCHEMA + pages + bootstrapper) |
| [`health-check.md`](health-check.md) | The periodic lint — bloat signals + how to run it |
| [`metodo/`](metodo/SCHEMA.md) | **The project workflow** — the boilerplate a new project starts from: structure, intake, canonical sources, publishing invariants (pt-BR) |

Implement the protocols however your agent supports it (slash commands, skills, a prompt library). In the reference setup they're Claude Code commands (`/new-plan`, `/close-plan`, `/wiki-setup`).

### Protocols: the source is the running copy, this repo is the release

**Canonical direction, declared so drift stops being an accident.** The protocols that *run* live in the agent's own config (here: `~/.claude/commands/`). They evolve every time a real project bites. `protocols/` in this repo is the **release** — a distilled, store-agnostic snapshot, updated at milestones.

**There is no promise of a mirror**, and that's deliberate: the promise is what produced the drift. Before this was declared, the three files sat at 72/114, 73/151 and 58/216 lines against their sources — the repo silently three months behind, with nothing saying which one was right.

- **Divergence with a declared direction is a version.** Divergence without one is a bug.
- The release is **shorter on purpose**: it drops vault paths, client names, and store-specific mechanics, and keeps the rule plus the bug that produced it.
- Sync flows **one way** (source → release), never back. A fix belongs in the running copy first, or the next session overwrites it.

## Two layers: the pattern, and the workflow

This repo is the **boilerplate you start a project from**, and it holds two layers that answer different questions:

- **The pattern** (`guide.md`, `protocols/`, `health-check.md`) — *how the wiki stays lean*: ingest, query, lint, and the anti-bloat loop. Agent- and domain-agnostic.
- **The workflow** ([`metodo/`](metodo/SCHEMA.md)) — *what the wiki documents*: how a project is structured, where new input goes, what is canonical, how the client-facing layer is written, what publishing refuses to publish. **Written in pt-BR**, and every rule arrives with the bug that produced it.

Two rules keep it a boilerplate instead of a scrapbook:

- **Referenced, never copied.** Each project's wiki **points at** `metodo/` and keeps only what is its own (`client`, `stack`, `conventions`). A cross-project rule duplicated inside a project wiki is a bug — copying the workflow per project is exactly the failure it exists to prevent.
- **Every rule carries its bug, and the bug carries no names.** The evidence is the situation and the number (*"the count ended up living in 11 files"*) — never the client, the city, or the competitor. A rule that only holds up by naming someone doesn't go in.

Not every project ends in code. The workflow declares [where a project stops](metodo/fases-e-agentes.md) — research and scope, layout, UI handed off, or live — and which parts switch off with it. Verification with no target is worse than no verification.

## Structure

```
<project>/
├── wiki/
│   ├── SCHEMA.md        ← read first every session; the map
│   ├── stack.md         ← tech, versions, env, commands
│   ├── conventions.md   ← critical rules & silent-failure gotchas
│   ├── database.md      ← schema, tables, access rules
│   ├── api.md           ← routes, endpoints, flows
│   ├── business.md      ← model, roadmap, what NOT to build
│   └── …                ← per-project pages
├── AGENTS.md            ← lean session bootstrapper (just enough to not err before reading the wiki)
├── log.md              ← append-only session history
└── plans/
    ├── open/  ├── closed/  └── _INDEX.md
```

*(The reference impl calls the bootstrapper `CLAUDE.md`; use whatever your agent reads at session start.)*

## Principles

- **One source of truth per fact** — the wiki holds it; the bootstrapper only bootstraps.
- **Append-only in the log, prunable in the wiki** — the log never rewrites; wiki pages describe current state and *replace/remove* what's obsolete on every ingest.
- **Lean by default** — a wiki page exists to be read whole in one session. If it grows without adding durable knowledge, that's pending pruning, not a healthy wiki.
- **Explicit links** — always path-qualified cross-links; never bare links that resolve ambiguously.

## Adopt it

1. Copy [`guide.md`](guide.md) into your project as the pattern reference.
2. Run the [`wiki-setup`](protocols/wiki-setup.md) protocol to bootstrap `SCHEMA.md` + pages.
3. Wire [`new-plan`](protocols/new-plan.md) / [`close-plan`](protocols/close-plan.md) as your open/close workflow — this is where the anti-bloat discipline gets enforced.
4. Schedule the [`health-check`](health-check.md) (e.g. monthly) as the safety net.

---

*The name — Daedalus, the mythic craftsman who held the middle course while Icarus flew too high and fell. A wiki that stays lean keeps the same discipline.*

*Living document — refined in practice across real projects. Issues and PRs welcome. Credit to Andrej Karpathy for the original pattern.*

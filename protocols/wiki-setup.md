# Protocol — wiki-setup

Bootstrap the LLM Wiki for a new project. Follow every step.

> Reference impl: a Claude Code slash command (`/wiki-setup`), writing to an Obsidian vault via an MCP server. Adapt the store to yours.
>
> **This file is the release, not the source** — see [Protocols: source and release](../README.md#protocols-the-source-is-the-running-copy-this-repo-is-the-release). Fix the running copy first.

## 0 — Read the method first

Read the shared method's `SCHEMA` and its structure page ([`metodo/`](../metodo/SCHEMA.md) here). **That's where the shape comes from** — this protocol is the executor, not the source.

⚠️ **A new wiki is born LINKING the method, never copying it.** No cross-project rule gets written inside a project's wiki: that wiki holds `client` · `stack` · `conventions` (the gotchas of **that** stack) and this project's **config**. Everything else points at the shared method.

The reason is the method's own first rule: **a method copied per client becomes six diverging versions in six months, and none of them is the source.** Test while writing: if you're explaining *why* a rule exists, that rule belongs to the method — link it and delete the text.

## 1 — Identify the project
Detect from the working directory and resolve exact paths. **Always confirm with the user** before creating anything — show the target structure (wiki location, bootstrapper, log, project folders) and wait for explicit confirmation.

## 2 — Check if a wiki already exists
Check whether `wiki/SCHEMA.md` exists. If yes, ask whether to recreate or update. If no, proceed.

## 3 — Explore the project
Read in parallel the files that reveal stack and purpose: `package.json` / `pyproject.toml` / `Cargo.toml` (deps + versions), `README.md`, any existing bootstrapper, top-level directory structure. Map: language, framework, database, infra, product purpose.

## 4 — Create the wiki pages
Always create:
- `SCHEMA.md` — wiki index + operations (ingest/query/lint)
- `stack.md` — tech stack, versions, env vars, commands (or tools, for non-code projects)
- `conventions.md` — critical patterns, rules that prevent errors/rework
- `business.md` — purpose, model, roadmap, what NOT to build now *(for client work, swap for `client.md`: brief, status, key contacts, goals, what's shipped)*

Create if applicable: `database.md` (schema/tables/access), `api.md` (routes/endpoints/crons), `brand.md` (voice/identity), plus any domain-specific page.

Each page:
- Starts with `# [Topic] — [Project]` and `Updated: [today]`
- Dense and scannable (tables, bullets, no long prose)
- References raw sources (docs/, README, code) for detail
- Ends with a nav line: `← [[SCHEMA]]`

**`SCHEMA.md`** contains, in order:

1. **The pointer to the shared method**, right below `Updated:` and before any table — stating that structure, intake, canonical sources, client-facing voice, publishing invariants and deliverable verification live there, and that **a cross-project rule reappearing here is duplication**.
2. `## Start here` — question → answer, one row per **single source** (project phase, what's pending, what's decided, the project map), plus a row for the method.
3. `## Pages` — table of each page (link + description).
4. `## By topic` — maps real questions to pages (lets the agent reach the right page without reading the whole SCHEMA):
   ```
   | Want to know about… | Read |
   |---|---|
   | Lib versions, env vars | [[stack]] |
   | Access rules, gotchas | [[conventions]] |
   | Routes, crons, webhooks | [[api]] |
   ```
5. `## Operations` — ingest/query/lint/query-back. Ingest always carries the line *"a cross-project rule goes to the method, never here"*; lint always carries *"…and a cross-project rule that came back"*.
6. `### Canonical source by topic — do not repeat` — a `topic → canonical` table, short and exhaustive. This is what makes a measured number get **cited** instead of copied. Always includes the project phase (one file, and only it).
7. `### This project's config` — the values the method declares as config rather than rule: title exceptions, terms that travel in pairs, research targets, this project's benchmark numbers, scan patterns. **Empty is acceptable; absent is not** — it's where the next project-specific value lands instead of becoming prose.

## 5 — Create the context files
- **Bootstrapper** (`AGENTS.md`/`CLAUDE.md`): 1-line description · current status · 1-line stack · wiki links (`[[wiki/SCHEMA]]` · pages) · history link. Plus, at the top, the session instruction: *"At session start, read `wiki/SCHEMA.md` and the pages relevant to the turn."*
- **`log.md`** (append-only): frontmatter + a first entry recording the wiki setup (pages created, stack detected, next steps).

## 6 — Scaffold the project structure
Standard project folders (create only what has content — no empty docs): a project note, `Briefings/`, `Specs/`, `plans/` (`open/` + `closed/` + `_INDEX.md`), `Meetings/`, `Decisions/`, `Assets/`. Never leave loose docs at the project root — each doc is born in its folder with type frontmatter.

**Three single-source files are born with the project, even with nothing in them yet:**

| File | What it is |
|---|---|
| `roadmap` | **the only place the project phase is stated.** Every other doc points at it |
| `PENDENCIAS` | what's open, **by owner and what it blocks** — internal and a superset; any client-facing mirror is only what needs their answer |
| `Decisions/_ESTADO` | what's true **today**, by topic, with `Current` / `History`. Dated decision records are events and aren't edited; reverting means editing `Current` here |

⚠️ **A single source that doesn't exist on day 1 gets created by accident on day 30, in two places.** Measured on a project that started without them: the phase ended up asserted by hand in **eight** files, and the pending items in **four** — the most complete of the four hidden inside a wiki page.

Each of the three is born with a pointer to the method in place of the long justification — the reason belongs to the method, the value belongs here.

## 7 — Trim the code repo's bootstrapper
If the repo already has an `AGENTS.md`/`CLAUDE.md`, use this moment to slim it — everything that migrated to the wiki no longer belongs there. Keep only: the wiki-read instruction, 1-line stack, dev/build/test commands, and the critical gotchas the agent must know *before* reading the wiki. The test: if it's lookup-able in the wiki, remove it; if the agent needs it to not err immediately, keep it.

## 8 — Confirm
List what was created and how to use it: *"read the wiki before starting"*, how to ingest, how to lint — plus the three single-source files.

**Before confirming, check (required):**
- **0 cross-project rules** written into the new wiki — no explanation of *why* a rule exists. If there is one, delete it and link the method
- the `SCHEMA` has the method pointer, the **canonical-source** table, and the **config** section
- the three single-source files exist, and **no other doc asserts the phase**
- **where this project stops** is declared (research & scope · layout · UI handed off · live) — it decides which of the method's verifications have a target, and verification with no target is worse than no verification

---

← [README](../README.md)

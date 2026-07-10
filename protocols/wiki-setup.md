# Protocol — wiki-setup

Bootstrap the LLM Wiki for a new project. Follow every step.

> Reference impl: a Claude Code slash command (`/wiki-setup`), writing to an Obsidian vault via an MCP server. Adapt the store to yours.

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
1. `## Pages` — table of each page (link + description).
2. `## By topic` — maps real questions to pages (lets the agent reach the right page without reading the whole SCHEMA):
   ```
   | Want to know about… | Read |
   |---|---|
   | Lib versions, env vars | [[stack]] |
   | Access rules, gotchas | [[conventions]] |
   | Routes, crons, webhooks | [[api]] |
   ```
3. `## Operations` — the ingest/query/lint/query-back commands.

## 5 — Create the context files
- **Bootstrapper** (`AGENTS.md`/`CLAUDE.md`): 1-line description · current status · 1-line stack · wiki links (`[[wiki/SCHEMA]]` · pages) · history link. Plus, at the top, the session instruction: *"At session start, read `wiki/SCHEMA.md` and the pages relevant to the turn."*
- **`log.md`** (append-only): frontmatter + a first entry recording the wiki setup (pages created, stack detected, next steps).

## 6 — Scaffold the project structure
Standard project folders (create only what has content — no empty docs): a project note, `Briefings/`, `Specs/`, `plans/` (`open/` + `closed/` + `_INDEX.md`), `Meetings/`, `Decisions/`, `Assets/`. Never leave loose docs at the project root — each doc is born in its folder with type frontmatter.

## 7 — Trim the code repo's bootstrapper
If the repo already has an `AGENTS.md`/`CLAUDE.md`, use this moment to slim it — everything that migrated to the wiki no longer belongs there. Keep only: the wiki-read instruction, 1-line stack, dev/build/test commands, and the critical gotchas the agent must know *before* reading the wiki. The test: if it's lookup-able in the wiki, remove it; if the agent needs it to not err immediately, keep it.

## 8 — Confirm
List what was created and how to use it: *"read the wiki before starting"*, how to ingest, how to lint.

---

← [README](../README.md)

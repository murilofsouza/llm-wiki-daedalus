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

Implement the protocols however your agent supports it (slash commands, skills, a prompt library). In the reference setup they're Claude Code commands (`/new-plan`, `/close-plan`, `/wiki-setup`).

### Protocols: the source is the running copy, this repo is the release

**Canonical direction, declared so drift stops being an accident.** The protocols that *run* live in the agent's own config (here: `~/.claude/commands/`). They evolve every time a real project bites. `protocols/` in this repo is the **release** — a distilled, store-agnostic snapshot, updated at milestones.

**There is no promise of a mirror**, and that's deliberate: the promise is what produced the drift. Before this was declared, the three files sat at 72/114, 73/151 and 58/216 lines against their sources — the repo silently three months behind, with nothing saying which one was right.

- **Divergence with a declared direction is a version.** Divergence without one is a bug.
- The release is **shorter on purpose**: it drops vault paths, client names, and store-specific mechanics, and keeps the rule plus the bug that produced it.
- Sync flows **one way** (source → release), never back. A fix belongs in the running copy first, or the next session overwrites it.

## This repo is the pattern, not your domain's method

**What this repo holds** (`guide.md`, `protocols/`, `health-check.md`) is *how the wiki stays lean*: ingest, query, lint, and the anti-bloat loop. Agent-, store- and domain-agnostic.

**What it deliberately does not hold** is *your* method — how a project of your kind is structured, what your delivery verifies, how your client-facing material is written. That belongs to a **shared method of your own**, one per domain, that your project wikis point at.

Keeping the two apart is not tidiness. They answer different questions and have different readers, and merged they tax each other: every page of a merged method opens by explaining when it does *not* apply. This repo carried a `metodo/` folder for exactly that reason and it grew to 2.5× the pattern before the split — [the split is recorded below](#history).

Two rules make a shared method work, whatever domain it covers:

- **Referenced, never copied.** Each project's wiki **points at** the shared method and keeps only what is its own (`client`, `stack`, `conventions`). A cross-project rule duplicated inside a project wiki is a bug — copying the method per project is exactly the failure it exists to prevent. Six months in you have six diverging versions and none is the source.
- **Every rule carries its bug, and the bug carries no names.** The evidence is the situation and the number (*"the count ended up living in 11 files"*) — never the client, the city, or the competitor. A rule that only holds up by naming someone doesn't go in.

And one that applies to both layers: **not every project ends in code.** Declare where yours stops — research and scope, layout, UI handed off, or live — and switch off the checks that lose their target. A `code_checked` field on a project with no code is an empty field that the next sweep reads as a pending task, and that trains people to ignore empty fields. **Verification with no target is worse than no verification.**

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

## History

**The `metodo/` folder was removed.** For a while this repo also carried the author's own project method — a UX-research delivery workflow, in pt-BR — as a second layer. It grew to 1,422 lines against the pattern's ~580: the guest was 2.5× the house, in a different language, for a different reader.

What made it a defect rather than a preference was measurable. Because the two halves never applied at the same time, the method needed traffic control: a whole section of its map, a four-stop table, and per-page banners existed only to say which half applied — **every page opened by explaining when it did not apply.** And it started breaking its own headline rule: *"one truth in two files is two sources"*, while one of its rules was stated five times in five files with none of them naming an owner, and one case study was narrated in full twice, copying a measured number along with it.

The root cause is the more useful lesson: **that method was the only piece of the system with no bottleneck.** The publishing script next to it works because it *aborts* — it sits where all content passes, so a wrong rule fails immediately. Prose has no such gate, and this repo's own guide already says it: *what has automated verification doesn't come back; what depends on reading comes back every time.* So it grew and duplicated unchecked.

Two things follow, and they're why this section exists instead of a silent deletion:

- **Splitting prose by folder is not the fix.** A split with no bottleneck on either side yields two better-organized bodies of unenforced prose. Each side has to declare what actually enforces it, and a rule no gate can ever check is a candidate for deletion, not relocation.
- **The criterion that authorized the merge was ours, and it was incomplete.** *"Divide by what the artifact is, not by subject"* separates prose from code well and is **blind to separating prose from prose**. The missing half: **who reads it, and what it is the source of.**

The method now lives in its own private repo with all 15 of its commits, and this one is back to a single subject. If you cloned this repo when `metodo/` was here, nothing was lost — it moved.

---

*The name — Daedalus, the mythic craftsman who held the middle course while Icarus flew too high and fell. A wiki that stays lean keeps the same discipline.*

*Living document — refined in practice across real projects. Issues and PRs welcome. Credit to Andrej Karpathy for the original pattern.*

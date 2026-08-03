# Protocol — new-plan

Open a new unit of work. Follow every step.

> Reference impl: a Claude Code slash command (`/new-plan`). Adapt paths/store to yours.
>
> **This file is the release, not the source** — see [Protocols: source and release](../README.md#protocols-the-source-is-the-running-copy-this-repo-is-the-release). Fix the running copy first.

## 1 — Identify project & context
Detect the current project from the working directory. Read `plans/_INDEX.md` to find the next plan number. The `log.md` lives at the project (or client) level, one level above `plans/`.

## 2 — Gather info
If the user described the work when invoking, use that. Otherwise ask:
- Name / description of the work?
- Type: `feature` | `fix` | `refactor` | `research` | `content` | `design`
- Why are we doing this? What problem does it solve?

A full-cycle project (content → design → dev) uses a single continuous sequence of plans in the same `plans/` — not separate folders per phase.

## 3 — Create the plan file
Path: `plans/open/PLAN-[NNN] — [Name].md`

```
---
status: open
created: [today]
project: [project name]
type: [type]
---

# PLAN-[NNN] — [Name]

## Context
[Why are we doing this? What problem does it solve? Expected impact?]

## Goals
- [ ] [primary goal]

## Technical decisions
[Architecture, libs, chosen approach — fill during implementation]

## Tasks
- [ ] [task 1]

## Learnings
[Fill on close — what differed from the plan, what we learned]
```

> Don't add a "Related wiki" section to the template — the `project` frontmatter already gives the contextual backlink. If you must reference a specific wiki page in the body, use a path-qualified link to avoid ambiguous resolution.

## 4 — Update `_INDEX.md`
Add a row to the table linking the plan file; update the "## Open" section.

## 5 — Append to `log.md`
```
## [date] — [Tag] PLAN-NNN started: [Name]
**Goal:** [one line]
```

**`[Tag]` = the project tag, required only for a multi-project client.** It's what lets you slice a client-level log by project (`grep "\[Redesign\]" log.md | tail -20`) and disambiguate `PLAN-NNN` — plan numbers are per project, the log is per client. Single-project client: no tag, there's nothing to disambiguate.

Add **at the end** — the log is chronological, newest always at the bottom, and old entries are never reordered. **Check the date of the last entry first:** if it's later than today, something got out of order — fix it instead of stacking on top.

**Scope of this entry:** only what the *planning* produced and the close won't repeat — the goal in one line, plus decisions taken, alternatives discarded, or research when there were any. Don't restate scope that already lives in the plan file. `close-plan` **rewrites this entry** rather than appending a second one, so treat it as a draft that survives by being merged.

In multi-project setups, **prefix the link with the sub-project** so it resolves unambiguously — a bare link resolves to whichever project the graph guesses.

## 6 — Git branch
If the project is a git repo, **create the task branch** (autonomous up to the PR; a task never starts on the deploy branch). Branch from the *updated* deploy branch only if the tree is clean; otherwise branch from HEAD and say so.
```
git checkout <deploy-branch> && git pull --ff-only
git checkout -b feature/PLAN-[NNN]-[short-kebab]
```
Prefix by type (`feature/`, `fix/`, `chore/`, `refactor/`…). `research` / no-code phases → no branch.

## 7 — Confirm
Report: file created, branch created (or why not), how to track (`- [x]` on tasks), how to close (`close-plan` when shipped).

---

← [README](../README.md)

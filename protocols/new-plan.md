# Protocol — new-plan

Open a new unit of work. Follow every step.

> Reference impl: a Claude Code slash command (`/new-plan`). Adapt paths/store to yours.

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
## [date] — PLAN-NNN started: [Name]
**Goal:** [one line]
```
Add **at the end** (the log is chronological — newest always at the bottom). In multi-project setups where plan numbers collide across sub-projects, **prefix the link with the sub-project** so it resolves unambiguously.

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

# Protocol — close-plan

Close a finished unit of work and **distill** it into the wiki. This is where the wiki stays lean — the anti-bloat rules below are the heart of the whole system.

> Reference impl: a Claude Code slash command (`/close-plan`).

## 1 — Identify the plan
Read `plans/open/`. One open → assume it. Many → list and ask. Named on invocation → use that.

## 2 — Check completeness
Count checkboxes (`- [x]` done / `- [ ]` pending). If any pending, warn and wait for confirmation.

## 3 — Collect learnings
Ask: "Any learning, key decision, or difference from the plan to record?" Fill the plan's `## Learnings`. **Query-back:** also check whether a valuable ad-hoc synthesis surfaced this session (a bug diagnosis, an approach comparison) that isn't in the plan or the wiki — if so, mark it for the ingest in step 4.

## 4 — Ingest into the wiki

⚠️ **A wiki page is NOT a changelog.** Per-plan history already lives in `log.md` (step 8) and `_INDEX.md` (step 7). The wiki records the **current state** of the system — not the sequence of how it got there. Treat every page as **prunable, not append-only**.

For each affected page:
1. Read the current page.
2. **Edit the matching section to describe the current state** — if the plan changed a behavior/field/rule already documented, **correct/replace** the existing text instead of adding a new paragraph beside it. If the plan made something obsolete (dropped column, revoked rule, removed operation), **delete the dead text** — at most a one-line "replaced by X (see log)" if it's a likely point of confusion.
3. Update `Updated:` **with the date only**, **one single line** — if an `Updated:` already exists, edit its date; **never stack a second `Updated:` line**. Never a summary of what changed there; that's the log's job.
4. Keep `## Related Plans` as a **list of raw links, no prose**:
   ```
   ## Related Plans
   - [[plans/closed/PLAN-NNN — Name]]
   ```
   - **Exactly ONE** `## Related Plans` section per page, at the end — if one exists, **edit it**; **never create a second** (not in the middle of the file).
   - No 1-line summaries, no "delivered", no migration numbers — that's already in `log.md`.
   - **Cap ~10-12.** Past that, drop plans superseded by more recent ones on the same topic — the page stays findable via `log.md` / `_INDEX.md`.
5. **Zero** `(TASK-NNN)` provenance tags in the body and **zero per-plan prose** ("round 3", "0 errors after fix", "delivered X", "found in TASK-Y"). The rule/state is durable; which plan introduced it — and the journey there — is *log* metadata, not wiki. Cross-refs to another wiki page are fine; cite a plan inline only when it **clarifies a likely point of confusion**, never as a provenance stamp.

**✅ Final ingest check (required, before step 5) —** on each touched page confirm:
- **1** `## Related Plans` (≤~12 raw links, at the end)
- **1** `Updated:` line (date only)
- **0** new `(TASK-NNN)` tags in the body · **0** QA-run/delivery prose
- no old fact coexisting with the new one that replaced it (internal contradiction)

If anything fails, **distill the page before closing** — don't push it to the periodic lint.

## 5 — Update the bootstrapper (only what's durable)
The session bootstrapper (`AGENTS.md`/`CLAUDE.md`) is **not** a changelog — it's read whole every session. Touch it only if something durable changed (deploy edge, branch, new repo, stack, a cross-project decision) or a *live* pending thread opened/closed. Keep any "in progress / pending" section to ~3-5 bullets, prunable — never paste a delivery summary here.

## 6 — Close the plan
Frontmatter → `status: closed` + `closed: [today]`. Move the file `plans/open/` → `plans/closed/`.

## 7 — Update `_INDEX.md`
Flip the row `open → closed`, add the close date, remove it from the "## Open" section.

## 8 — Append to `log.md`
```
## [date] — PLAN-NNN closed: [Name]
**Delivered:** [1-2 line summary of what shipped]
**Wiki updated:** [[wiki/page1]] · [[wiki/page2]]
**Learnings:** [the most important point]
```
Add **at the end** (chronological, newest at the bottom). Never reorder old entries.

## 9 — Git branch
If a matching `feature/PLAN-NNN-*` branch exists: merged → suggest deleting local/remote; open PR → remind the deploy step is pending (never merge the deploy branch without explicit OK). No branch → nothing to do.

## 10 — Confirm
Report: plan archived, wiki pages updated (which), log updated, bootstrapper touched or not, branch status.

---

## Auto-detect (invoked with no argument at session end)
If the user didn't name a plan but the session shipped something: read the conversation to identify what was delivered, find the matching open plan, verify goals/tasks match, and propose: "Looks like PLAN-NNN is done — close it?"

---

← [README](../README.md)

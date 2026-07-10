# Health check — the reactive guardrail

The close-out protocol keeps the wiki lean **at ingest time** (proactive). This is the other half: a **periodic sweep** that catches drift the close-out missed (reactive). Run it on a schedule (e.g. monthly, headless) or on demand.

It only reads and reports — it edits nothing except an output report. Distillation stays a human-approved step.

## Bloat signals (what it flags)

A wiki page is drifting into a changelog when any of these appear:

| Signal | Why it's a smell |
|---|---|
| `## Related Plans` list **> ~15** entries | Backlink list growing unbounded — old, superseded plans never pruned |
| **> 1** `## Related Plans` section on a page | Ingest *appended* a new section instead of editing the existing one |
| **> 1** `Updated:` line on a page | Same — stacked changelog headers instead of one date |
| **Dense** `(TASK-NNN)` provenance tags in the body | Rules stamped with which plan introduced them — that's log metadata, not wiki |
| `Updated:` date **older** than the last closed plan for the project | Page went stale — code moved, wiki didn't |
| Broken / bare cross-links | Links that resolve ambiguously or 404 |

The threshold on the backlink list is deliberately generous (~15, not the ~10-12 ingest cap) so the sweep flags genuine bloat, not a page that's legitimately a touch over.

## How to run it

**On demand — parallel agents (thorough):** one sub-agent per wiki page, each returning specific proposed edits (no free-form commentary). Collect, present grouped by page, apply one page at a time with approval. Never delete durable content — only correct, trim, or flag.

**Scheduled — headless (a heartbeat):** a read-only run that writes a report (stale pages, oversized backlink lists, duplicate sections, dense tags, broken links) to a single health file, overwriting. It edits nothing else. In the reference setup this is a monthly cron/launchd job.

## Detecting the signals (shell sketch)

For a quick scriptable pass over a page's markdown (adapt to your store):

```sh
# entries in the Related Plans list
awk '/^## Related Plans/{f=1;next} /^## /{f=0} f&&/^- /{c++} END{print c+0}' page.md
# number of Related Plans sections
grep -c '^## Related Plans' page.md
# number of Updated: lines
grep -c '^Updated:' page.md
# provenance tags in the body (before Related Plans)
awk '/^## Related Plans/{exit} /\(TASK-[0-9]/{c++} END{print c+0}' page.md
```

Flag a page when: list > 15, sections > 1, `Updated:` > 1, or body tags dense (> ~8). A clean page trips none.

## The fix, when a page trips

Distill it — the same moves the close-out protocol enforces:
1. Rewrite the section to **current state**; delete superseded/contradicting text.
2. Collapse stacked `Updated:` → one date; merge duplicate `## Related Plans` → one, at the end.
3. Strip `(TASK-NNN)` tags and per-plan prose from the body — the journey lives in the log.
4. Trim the backlink list to the ~10-12 that map to current page content.

Nothing durable is lost: every dropped plan stays in the log and the plan index.

---

← [README](README.md)

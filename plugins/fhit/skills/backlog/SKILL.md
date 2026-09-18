---
name: backlog
description: Slices an intent with approved decisions into a sprint backlog and drafts one brief per sprint. Human approves the backlog (briefs included). Re-run after /refine to update.
disable-model-invocation: true
argument-hint: <intent number>
---

# Backlog for intent $ARGUMENTS

Pre-condition: `decisions.md` is `stage: approved`. Otherwise stop → `/fhit:intent` or `/fhit:refine`.

## 1. Slice
Read `decisions.md`, `research.md`, and an existing `backlog.md` (keep `done`/`running` sprints untouched).
Spawn `architect` in slice mode: given decisions + research, propose dependency-ordered sprints. Every proposal must contain a one-sentence Task for the backlog table, a Task description for the brief, an Outcome, dependencies, and the `Dn` it realises. Test every proposal against the sprint definition:
- Outcome satisfies the `workflow` glossary definition
- zero open decisions; independently mergeable; ~1–2 h; vertical slice
"Build X" is never an outcome.

## 2. Draft backlog and briefs
Write `backlog.md` from its template as a table with `#`, `Task`, `Depends on`, `Issue`, and `Status` columns. Each Task cell is one short sentence describing the Sprint's scope and product value. It must be understandable and verifiable by a PM/PO without code knowledge. Do not put the Outcome, acceptance criteria, decisions, assumptions, or implementation detail in the table.

For every `open` row, write `sprints/NN-<slug>/brief.md` from its template. `## Task` must be the first `##` section. It expands the backlog row's one-sentence Task into the complete 1–3 sentence Task description; the two must agree in scope but need not use identical text. Derive criteria from the Task, Outcome, and decisions; `Assumptions` = product-invisible calls you foresee. Before approval, reject any brief with a missing, empty, or duplicated-as-Outcome Task.

## 3. Approve
Show `backlog.md`; briefs available on request. The human judges the Tasks, order, cuts, size, and gaps (every `Dn` covered). Approves / reorders / cuts / merges → backlog and briefs `stage: approved`. This is the last human gate before the PR.

## 4. Publish
After approval only: `git-flow → Provider steps → Backlog approved`. Without a provider the `Issue` column is `–` and the step is done.
With a provider: milestone exists (create it if `/fhit:intent` skipped it), then one issue per `open` row — title = a concise form of its Task, body = the brief's Task + Outcome + Acceptance criteria + brief path, milestone assigned. Issue number → the row's `Issue` column. Row already carrying an issue → update it, never create a second one. `running`/`done` rows untouched.

## 5. Report
What the product will be able to do when the backlog is through, in one sentence, plus the milestone link (with a provider) and the next command `/fhit:sprint III` (all) or `/fhit:sprint III 01`.

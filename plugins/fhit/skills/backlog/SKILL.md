---
name: backlog
description: Slices an intent with approved decisions into sprint outcomes and drafts one brief per sprint. Human approves the backlog (briefs included). Re-run after /refine to update.
disable-model-invocation: true
argument-hint: <intent number>
---

# Backlog for intent $ARGUMENTS

Pre-condition: `decisions.md` is `stage: approved`. Otherwise stop → `/fhit:intent` or `/fhit:refine`.

## 1. Slice
Read `decisions.md`, `research.md`, existing `backlog.md` (keep `done`/`running` lines untouched).
Spawn `architect` in slice mode: given decisions + research, propose sprint outcomes, dependency-ordered. Test each against the sprint definition:
- one true/false statement, verifiable by using the product in ≤10 min
- zero open decisions; independently mergeable; ~1–2 h; vertical slice
"Build X" is never an outcome.

## 2. Draft briefs
For each `open` line: `sprints/NN-<slug>/brief.md` (template). Criteria from the outcome + decisions; `Assumptions` = product-invisible calls you foresee. ≤30 lines each.

## 3. Approve
Show `backlog.md`; briefs available on request. The human judges order, cuts, size (≤10 min to verify by using the product), gaps (every `Dn` covered). Approves / reorders / cuts / merges → backlog and briefs `stage: approved`. This is the last human gate before the PR.

## 4. Publish to the tracker
After approval only, per `git-flow → Tracker`: ensure the intent milestone exists (create if `/fhit:intent` skipped it), then one issue per `open` backlog line — title = outcome, body = the brief's outcome + acceptance criteria + brief path, milestone assigned. Write the issue number into the line's `Issue` column.
Line already carrying an issue → update its title/body instead of creating a second one. `running`/`done` lines untouched. No git remote → skip the whole step, note it.

## 5. Report
≤5 lines: sprint count, milestone URL, next command `/fhit:sprint III` (all) or `/fhit:sprint III 01`.

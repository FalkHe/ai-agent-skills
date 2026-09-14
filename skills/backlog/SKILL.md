---
name: backlog
description: Slices an intent with approved decisions into sprint outcomes and drafts one brief per sprint. Human approves the backlog (briefs included). Re-run after /refine to update.
disable-model-invocation: true
argument-hint: <intent number>
---

# Backlog for intent $ARGUMENTS

Pre-condition: `decisions.md` is `stage: approved`. Otherwise stop → `/intent` or `/refine`.

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

## 4. Report
≤5 lines: sprint count, next command `/sprint III` (all) or `/sprint III 01`.

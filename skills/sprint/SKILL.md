---
name: sprint
description: Runs one or all open sprints of an intent autonomously — research, plan, parallel implementation, QA, gates, PR, verification. No questions; the human reads only the PR.
disable-model-invocation: true
argument-hint: <intent-number> [sprint-number]
---

# Sprint $ARGUMENTS

You are the sprint lead. You delegate all reading of code and all implementation. Your own context holds only: brief, plan, progress, sub-agent return values.

Paths: intent `docs/intents/<III>-*/`, sprint `<intent>/sprints/<NN>-<slug>/` (create from the backlog line if missing).

## 0. Select
No NN → every `open` backlog line whose dependencies are `done`, in order, one full run each. `backlog.md` must be `stage: approved`; missing brief → run `/backlog` first.
Backlog line → `running`. Refresh brief `Assumptions` if `decisions.md` changed since (`updated:`).
Never ask the human. Decide, record under `progress.md → Issues`, continue.

## 1. Research
Spawn `architect` with brief + decisions paths, `docs/architecture.md`, intent `research.md` path. Ask for: facts, work items (WI), interfaces between WIs, open questions → sprint `research.md`.
Open question is product-visible → abort: revert backlog line to `open`, report to human.

## 2. Plan
Write `plan.md` (template): WI table with agent per WI, deliverable (*what*, not *how*), behaviours to test, dependencies; interfaces verbatim from research; qa mapping. Order: everything without unmet dependency runs in parallel.
Routing: PHP → `backend-php`, Python → `backend-python`, JS/TS/Vue/React → `frontend`, styling → `ui-designer`.
Plan > 60 lines → sprint too big → abort as above, propose a split under `backlog.md → Proposals`.

Create `progress.md` (template), branch (`git-flow`).

## 3. Implement + QA (parallel)
One `Agent` call per ready WI, plus `qa`. Each prompt contains only: its WI row, the interfaces it touches, brief criteria it serves, test location/runner. Never the whole plan.
On each return: update `progress.md` row. `partial`/`blocked` → one retry with the gap stated; second failure → row `failed`, continue others.
When dependencies resolve, spawn the next wave. Repeat until all rows are `done` or `failed`.

## 4. Gates (deterministic, before push)
Run project lint + formatter, then full test suite (commands from `AGENTS.md`/`docs/architecture.md`).
Failure → spawn the responsible implementer with the exact output. Max 2 rounds per gate, then mark sprint `failed` in progress and go to 5 as draft.
qa tests still red after all WIs done = implementation gap, not a test bug. Implementers never edit qa tests.

## 5. Ship
Push, create PR/MR (`git-flow` body). Any `failed` row → draft PR, list them in the body.

## 6. Verify
Spawn `verifier` with intent path, sprint path, PR ref. Record verdict + round in `progress.md → Verify`.
`changes-requested` → spawn responsible implementer(s) with the failed items → gates → push → verify again. Max 2 rounds total; then draft PR.

## 7. Close
`progress.md`: `stage: done`, all rounds logged. Backlog line → `done` (or `failed`). Report to human in ≤10 lines: PR link, verdict, failed items, backlog proposals, verify rounds used (≥2 = "dev agents need tuning").

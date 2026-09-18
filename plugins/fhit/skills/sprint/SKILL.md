---
name: sprint
description: Runs one or all open sprints of an intent autonomously — research, plan, parallel implementation, QA, gates, review, verification. No questions; the human reads only the review.
disable-model-invocation: true
argument-hint: <intent-number> [sprint-number]
---

# Sprint $ARGUMENTS

You are the sprint lead. You delegate all reading of code and all implementation. Your own context holds only: brief, plan, progress, sub-agent return values.

Paths: intent `docs/intents/<III>-*/`, sprint `<intent>/sprints/<NN>-<slug>/` (create from its backlog row if missing).
Provider: `AGENTS.md → Workflow` (`git-flow → Setup`). ≠ `none` → load the provider skill once, now.

## 0. Select
No NN → every `open` backlog row whose dependencies are `done`, in order, one full run each. `backlog.md` must be `stage: approved`; missing brief → run `/fhit:backlog` first.
Before starting, require a non-empty `## Task` as the first `##` section in the brief and a matching backlog row. If either is missing or their scopes conflict, stop and run `/fhit:backlog` to repair the planning artifacts.
Backlog row → `running`; note its `Issue` (`–` without a provider) and the milestone title (`<III>-<slug>`) for step 5. Refresh brief `Assumptions` if `decisions.md` changed since (`updated:`).
Never ask the human. Decide, record under `progress.md → Issues`, continue.

## 1. Research
Spawn `architect` with brief + decisions paths, `docs/architecture.md`, intent `research.md` path. Ask for: facts, work items (WI), interfaces between WIs, open questions → sprint `research.md`.
Open question is product-visible → abort: revert the backlog row to `open`, report to human.

## 2. Plan
Write `plan.md` (template): WI table with agent per WI, deliverable (*what*, not *how*), behaviours to test, dependencies; interfaces verbatim from research; qa mapping. Order: everything without unmet dependency runs in parallel.
Routing: PHP → `backend-php`, Python → `backend-python`, JS/TS/Vue/React → `frontend`, styling → `ui-designer`.
Plan over its word cap → sprint too big → abort as above, propose a split under `backlog.md → Proposals`.

Create `progress.md` (template), branch (`git-flow`).

## 3. Implement + QA (parallel)
One `Agent` call per ready WI, plus `qa`. Each prompt contains only: its WI row, the interfaces it touches, brief criteria it serves, test location/runner. Never the whole plan.
On each return: update `progress.md` row. `partial`/`blocked` → one retry with the gap stated; second failure → row `failed`, continue others.
When dependencies resolve, spawn the next wave. Repeat until all rows are `done` or `failed`.

## 4. Gates (deterministic, before shipping)
Run project lint + formatter, then full test suite (commands from `AGENTS.md`/`docs/architecture.md`), including the browser acceptance suite where one is configured. The app must be running for it (`docker-dev`).
Failure → spawn the responsible implementer with the exact output. Max 2 rounds per gate, then mark sprint `failed` in progress and go to 5 as draft.
qa tests still red after all WIs done = implementation gap, not a test bug. Implementers never edit qa tests.

## 5. Ship
Write `review.md` (template; content per `git-flow → Review`); every `failed` row → one sentence under `Heads-up`. Commit `docs(sprint-<NN>): review`.
Then `git-flow → Provider steps → Sprint shipped`: push; with a provider, the PR/MR carrying milestone, issue, reviewer and body — draft when a row failed; its URL → `review.md` frontmatter `url:`. Sprint has no issue although a provider is set → create it now (`Backlog approved`).

## 6. Verify
Spawn `verifier` with intent path, sprint path, branch, and the PR/MR ref when `url:` has one. From its return value: product owner's sentences → `review.md → Verdict` (round n); round, verdict and failed criteria → `progress.md → Verify`. Commit `docs(sprint-<NN>): verdict n`; push when the branch was pushed.
`changes-requested` → spawn responsible implementer(s) with the failed items → gates → ship again (step 5 without a new PR/MR) → verify again. Max 2 rounds total; then the last verdict stands and the PR/MR becomes draft (provider `Verdict`).

## 7. Close
`progress.md`: `stage: done`, all rounds logged. Backlog row → `done` (or `failed`). Merging is the human's (`git-flow → Merge`); never merge, never close an issue.
Report to the human: what the product can now do, in one sentence, plus the review link — PR/MR URL, or the branch name without a provider. Anything failed, cut, assumed, or proposed → one sentence each, as a user would notice it. Nothing of the kind → the one sentence and the link, nothing else. Two or more verify rounds is such a thing: say the dev agents need tuning.

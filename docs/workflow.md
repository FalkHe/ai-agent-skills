# Development Workflow

Reference for the agent stack. Agreed 2026-09-14; discussion in `discussion-roadmap.md`.

## Terms

- **Intent**: one human wish, any roughness ("we need user auth"). Spans 1..n sprints.
- **Sprint**: one outcome, implemented in one autonomous agentic run.
- **Decision**: fixed by a human. Only a human may change it.
- **Conclusion**: derived by an agent from decisions. Mutable. Must cite its source (`← D3`) or be marked `ASSUMPTION`.
- **Human layer**: docs a human must read. ≤50 lines each (hard max 100).
- **Agent layer**: docs between agents. Human never required to read.

## Principles

1. Product user would notice the difference → human decision. Otherwise → agent conclusion, listed for veto only.
2. Agent-layer docs are handoff contracts, not transcripts. One consumer per doc. Point to code (`path:line`), don't paste it.
3. Sub-agent I/O (prompt in, return out) is never written to disk. Files exist only where a fresh-context boundary is crossed or resume is needed.
4. Source of truth = code + module READMEs. Never past sprints.
5. Prefer parallelism → interfaces between work items are fixed before implementation.

## Layout

```
AGENTS.md                         ≤60 lines: role, where things are, how to run tests. CLAUDE.md = @AGENTS.md
docs/architecture.md              current state, facts only ("What → what for"). Sections: Tech stack, Structure, Conventions, Infrastructure
docs/intents/001-<slug>/
  intent.md                       H  human input verbatim + date. Immutable
  research.md                     A  options, landscape, codebase facts
  decisions.md                    H  index D1..Dn, one line each
  decisions/Dn-<slug>.md          H  attachment per decision (wireframe, data model, wording). No cap
  backlog.md                      H  sprint outcomes, one line each, dependency-ordered
  sprints/01-<slug>/
    brief.md                      H  ≤30 lines: outcome, acceptance criteria, ← decisions, assumptions
    research.md                   A  implementation facts, work-item slicing, interfaces
    plan.md                       A  ≤60 lines: work items, order, deliverable + test per item, interface contracts
    progress.md                   A  sprint state: items done/undone, ≤1-line note each
```
H = human layer, A = agent layer. No spaces in paths. Intents 3 digits, sprints 2.

Every file in `docs/intents/` carries YAML frontmatter:

```yaml
---
author: human | intake-agent | sprint-agent | verifier   # who wrote it
owner: human | agent                                      # who may modify
created: 2026-09-14
updated: 2026-09-14
stage: draft | approved | done                            # where it makes sense
---
```
No `version` field — git has it.

## Phase 1: Intent (human in the loop)

Trigger: prompt (`/intent`, chat) or issue (`/issue`, same gates via issue comments: `approve`, `Dn: <answer>`, `stop`).

1. **Capture** input verbatim → `intent.md`.
2. **Research** options + codebase → `research.md`. Present ≤20 lines: options, trade-offs, recommendation.
3. **Roast** — a conversation with the human. Agent asks product-visible questions; designers/architect only supply proposals. Each answer → one line in `decisions.md` (+ attachment).
4. **Approve decisions**: human reviews and refines `decisions.md` → `stage: approved`.
5. **Backlog** (`/backlog`): propose sprint outcomes + draft briefs → human judges order, cuts, size, gaps → approved. Last gate before the PR.

Human reads: intent, research summary, decisions, backlog.

## Phase 2: Sprint (autonomous)

Pre-condition: backlog approved. Zero open human decisions. `/sprint III` runs all open sprints in order; no questions.

| Stage | Actor | Output |
|---|---|---|
| Research | sub-agent | `research.md`: facts for this outcome, work items, interfaces. Reads intent `research.md` first |
| Plan | main agent | `plan.md`. No implementation detail (paths, methods, names) except interface contracts between work items |
| Implement | sub-agents per work item, parallel where possible | code + unit/integration tests, one commit per item. Main agent passes only the bits each item needs. Self-check = lint/format only |
| QA | `qa` sub-agent, parallel to Implement | acceptance tests, one per criterion, black-box, derived from brief + interface contracts — never from code |
| Ship | main agent | lint + full test suite green (fix inside team, never push red) → push branch `sprint/<intent>-<sprint>-<slug>`, PR/MR ≤20 lines: outcome, criteria, assumptions, link to brief |
| Verify | separate agent, fresh context | LLM judgement on the PR/MR (see below); approve or request changes |

Main agent keeps `progress.md` current: state + short notes ("done, 3 tests", "sub-agent improvised", "missed goal").
Mid-sprint discoveries → proposed backlog item, never sprint growth.
Plan/research exceeding caps = sprint too big → report, stop.

### Tests — no test without a reason

- Every test names its reason: criterion (`← AC2`) or behaviour ("rejects expired session"). No reason → verifier deletes it
- `plan.md` lists behaviours to test per work item. Implementer tests those; more only with a reason
- Red first: `test:` commit failing before `feat:` commit passes. A test that never failed proves nothing
- Implementer never edits `qa` tests. Disagreement → note in `progress.md`, main agent decides
- Verifier asks per test: would it fail if the feature were removed? Does it assert real behaviour, not mocks?
- Optional per project (`architecture.md`): mutation testing (Infection, mutmut, Stryker) on critical modules
- Minimal overhead: one acceptance test per criterion, unit tests only for logic that can be wrong. No tests for framework glue

### Gates

Deterministic, in Ship, before push: lint/format → tests (new behaviour covered, suite green). Break on first failure → back to team.

LLM judge, in Verify, on the PR/MR: each acceptance criterion vs. brief + decisions; no secrets / debug leftovers; module README + `architecture.md` updated if touched; `progress.md` complete; PR body present.
Result posted as review (approve / request changes; needs own account, else comment). Max 2 fix rounds → mark PR draft, list failed criteria. **Merge = human only.**

## Sprint definition

- Outcome: one true/false statement verifiable by using the product (or a scoped dev-check) in ≤10 min
- Hard: zero open decisions at start; PR independently mergeable
- Guideline: 1–2 h; vertical slice; reverting the PR removes exactly this feature
- "build login" = intent. "sign in with email+password, no verification mail" = sprint. "error state on wrong password" = acceptance criterion

## Tooling

Claude Code first (`.claude/agents`, `.claude/skills`, `Agent` tool). Instructions harness-neutral markdown for porting to Codex / Gemini CLI. Commits: conventional (`feat:`, `fix:`, `test:`). PRs via `gh` / `glab`.

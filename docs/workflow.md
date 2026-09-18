# Development Workflow

Reference for the agent stack. Agreed 2026-09-14; discussion in `discussion-roadmap.md`.

## Glossary

- **Intent**: one human wish, any roughness ("we need user auth"). Spans 1..n sprints.
- **Task**: the product work to review, build, or change. It may be decomposed into agent-layer work items or subtasks during planning.
- **Sprint**: one Task, or a coherent group of subtasks that completes it, delivered in one autonomous agentic run.
- **Backlog**: the list of Sprints/Tasks belonging to one Intent, stored in `backlog.md`.
- **Outcome**: the true/false statement a PM/PO can verify by using the product or reviewing the merge request in ≤5 min to confirm that the Sprint's Task is complete.
- **Decision**: fixed by a human. Only a human may change it.
- **Conclusion**: derived by an agent from decisions. Mutable. Must cite its source (`← D3`) or be marked `ASSUMPTION`.
- **Human layer**: docs and messages a human reads. Reader = the product owner: knows the product, has not read the code. Best 250 words, max 500.
- **Review**: the pushed sprint branch plus `review.md` — what the product owner reads before merging. With a provider also the PR/MR, whose body is `review.md`.
- **Provider**: GitHub, GitLab or `none`, set per project in `AGENTS.md → Workflow` by `/fhit:init` together with the two accounts (`Human` = product owner, reviewer and merger; `Agent` = the account the CLI is signed in as). A provider mirrors milestone per Intent, issue per Sprint, PR/MR per Sprint; `docs/intents/` and git stay the source of truth and work alone.
- **Agent layer**: docs between agents. Human never required to read. `research.md` best 500 / max 1000 words, `plan.md` best 250 / max 500.

## Principles

1. Product user would notice the difference → human decision. Otherwise → agent conclusion, listed for veto only.
2. Agent-layer docs are handoff contracts, not transcripts. One consumer per doc. Point to code (`path:line`), don't paste it.
3. Sub-agent I/O (prompt in, return out) is never written to disk. Files exist only where a fresh-context boundary is crossed or resume is needed.
4. Source of truth = code + module READMEs. Never past sprints.
5. Prefer parallelism → interfaces between work items are fixed before implementation.
6. Plain git is the baseline; every provider object mirrors exactly one file-layer object. No granularity exists in the tracker that does not exist in `docs/intents/`.
7. Report exceptions, not progress. A run that went as planned is one sentence and a link; only what the human must act on gets more. Failures are named by what a user would notice, never by where they broke.
8. Text is measured in words (`wc -w`), never in lines — a line cap is met by writing one very long line, which is worse, not better.

## Layout

```
AGENTS.md                         ≤400 words: role, where things are, how to run tests, Workflow: Provider / Human / Agent. CLAUDE.md = @AGENTS.md
docs/architecture.md              current state, facts only ("What → what for"). Sections: Tech stack, Structure, Conventions, Infrastructure
docs/intents/001-<slug>/
  intent.md                       H  human input (pointers resolved) + date + milestone url. Immutable
  research.md                     A  options, landscape, codebase facts
  decisions.md                    H  index D1..Dn, one sentence each
  decisions/Dn-<slug>.md          H  attachment per decision (wireframe, data model, wording). No cap
  backlog.md                      H  table of dependency-ordered Sprints with short Tasks, issue and status
  sprints/01-<slug>/
    brief.md                      H  Task first, then Outcome, acceptance criteria, ← decisions, assumptions
    research.md                   A  implementation facts, work-item slicing, interfaces
    plan.md                       A  work items, order, deliverable + test per item, interface contracts
    progress.md                   A  sprint state: items done/undone, ≤1-line note each
    review.md                     H  what changed, how to check it, heads-up, verdict per round. PR/MR body with a provider
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

Trigger: prompt (`/fhit:intent`, chat) or issue (`/fhit:issue`, same gates via issue comments: `approve`, `Dn: <answer>`, `stop`).

1. **Capture** input → `intent.md`. Self-contained text verbatim; a pointer (file, path, URL, issue) is read and the wish restated from its content, with `source:` recorded.
2. **Research** options + codebase → `research.md`. Present options, trade-offs, recommendation — in product terms.
3. **Roast** — a conversation with the human. Agent asks product-visible questions; designers/architect only supply proposals. Each answer → one sentence in `decisions.md` (+ attachment).
4. **Approve decisions**: human reviews and refines `decisions.md` → `stage: approved`. With a provider: milestone `<III>-<slug>` created, URL into `intent.md`.
5. **Backlog** (`/fhit:backlog`): write one short Task per Sprint into the `backlog.md` table and draft the complete briefs → human judges Tasks, order, cuts, size, gaps → approved. Last gate before the review. With a provider, after approval each `open` row gets an issue on the milestone, assigned to `Agent`.

Human reads: intent, research summary, decisions, backlog.

## Phase 2: Sprint (autonomous)

Pre-condition: backlog approved. Zero open human decisions. `/fhit:sprint III` runs all open sprints in order; no questions.

| Stage | Actor | Output |
|---|---|---|
| Research | sub-agent | `research.md`: facts for this outcome, work items, interfaces. Reads intent `research.md` first |
| Plan | main agent | `plan.md`. No implementation detail (paths, methods, names) except interface contracts between work items |
| Implement | sub-agents per work item, parallel where possible | code + unit/integration tests, one commit per item. Main agent passes only the bits each item needs. Self-check = lint/format only |
| QA | `qa` sub-agent, parallel to Implement | acceptance tests, one per criterion, black-box, derived from brief + interface contracts — never from code |
| Ship | main agent | lint + full test suite green (fix inside team, never push red) → `review.md` in the product owner's language: what changed, how to check it, heads-up if any, link to brief → push branch `sprint/<intent>-<sprint>-<slug>`. Provider: PR/MR with `review.md` as body, milestone, reviewer `Human`, `Closes #<issue>` |
| Verify | separate agent, fresh context | LLM judgement on the review (see below): approve or changes requested → `review.md → Verdict`; provider: posted as PR/MR review |

Main agent keeps `progress.md` current: state + short notes ("done, 3 tests", "sub-agent improvised", "missed goal").
Mid-sprint discoveries → proposed backlog Sprint, never sprint growth.
Plan/research exceeding its word cap = sprint too big → report, stop.

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

LLM judge, in Verify, on the review: each acceptance criterion vs. brief + decisions; no secrets / debug leftovers; module README + `architecture.md` updated if touched; `progress.md` complete; `review.md` present.
Verdict in the product owner's language, failures only, appended to `review.md`; with a provider also posted as PR/MR review (approve / request changes; needs its own account, else comment). Max 2 fix rounds → PR/MR draft, failed criteria listed. **Merge = human only** — `git merge --no-ff` without a provider; with one, the merge closes the sprint issue, so the milestone's progress equals merged sprints.

## Sprint definition

- Task: 1–3 sentences describing what the sprint must build or change and the relevant scope. It is actionable work, not a restatement of the Outcome or an acceptance-criteria list
- Outcome: follows the glossary definition above
- Hard: zero open decisions at start; PR independently mergeable
- Guideline: 1–2 h; vertical slice; reverting the PR removes exactly this feature
- "build login" = intent. "sign in with email+password, no verification mail" = sprint. "error state on wrong password" = acceptance criterion

## Tooling

Claude Code first (`.claude/agents`, `.claude/skills`, `Agent` tool). Instructions harness-neutral markdown for porting to Codex / Gemini CLI. Commits: conventional (`feat:`, `fix:`, `test:`).

Provider handling: `git-flow` is provider-agnostic and names the steps (intent approved, backlog approved, sprint shipped, review diff, verdict, status, merge), each with a plain-git default. The `github` and `gitlab` skills implement the same section names with `gh` / `glab`; a command loads the one `AGENTS.md → Workflow` names. Adding a provider = one new skill, no change elsewhere.

Tracker objects use the stable issue API on both platforms (`gh issue` / `glab issue`), not GitLab work items: `glab work-items` is experimental, absent before glab ~1.60, and its `create` has no `--milestone`. GitLab renders issues as work items in the UI regardless, so nothing is lost. Milestones are created through `gh api` / `glab api` for the same version-independence.

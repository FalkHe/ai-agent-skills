# Discussion Roadmap: Development Workflow

Status: `[ ]` open · `[~]` in discussion · `[x]` agreed

**All topics agreed (2026-09-14).** Condensed into `workflow.md`.

## 1. Input `[x]`
- [x] Granularity: one entry point; intake step classifies (project / feature / bugfix)
- [x] Format:
  - (a) Prompt: human-in-the-loop discussion (often long, "roasting" the idea)
  - (b) `request.md`: agent-written, refined during (a); input for all later agents/stages
  - (c) Issue (gh/glab): optional trigger for one-shot automated tasks
- [x] Clarification: prompt-triggered (a) → interactive chat. Issue-triggered (c) → same approval gates, but via issue comments (`approve`, `Dn: <answer>`, `stop`). Decisions and backlog are always human-approved; agents only propose

## Principles (agreed)
- **Two doc layers, bold line between them**
  - Human ↔ Agent: ≤ 50 lines (hard max 100). Decisions, not detail.
  - Agent ↔ Agent: as long as needed. Human never required to read.
- **Decisions vs. conclusions, kept apart**
  - Human decisions: high level, fixed. Only a human may change them.
  - Agent conclusions: derived from human input, mutable. Must state which decision they derive from, so a wrong conclusion is traceable to a misunderstanding.

## 2. High-level workflow `[x]`
- Term: **Sprint** = one "thing" implemented in a single autonomous agentic run.
- [x] Sprint definition
  - Outcome: one true/false statement a human can verify by using the product (or a scoped dev-check) in ≤10 min
  - Hard: zero open human decisions at start; PR independently mergeable
  - Guideline: 1–2 h wall-clock (agent team); vertical slice (all layers); reverting the PR removes exactly this feature
  - Example: "sign-in with user/pass" = sprint; "error state on wrong password" = acceptance criterion inside it
- [x] Idea → Sprints (human loop)
  1. **Idea**: any roughness ("we need user auth"). Agent researches options + codebase, then roasts/brainstorms with human
  2. **Decisions**: captured during 1. Rule: *if a product user would notice the difference → human decision; else → agent conclusion (veto-able)*
  2b. **Approve decisions**: human reviews/refines `decisions.md` before any slicing
  3. **Backlog** (`/backlog`): agent proposes sprint outcomes, one line each, dependency-ordered. Human approves/reorders/cuts. Mutable after each sprint; decisions are not
  4. **Sprint brief** (≤30 lines: outcome, acceptance criteria, relevant decisions, assumptions) drafted with the backlog, approved with it. `/sprint` runs without a gate. Human gates: decisions, backlog, PR merge
  - "build login" = idea, not sprint. Sprint = "sign in with email+password, no verification mail"
  - Mid-sprint discoveries → proposed backlog item, not sprint growth
  - Human reads only: decisions, backlog, one brief at a time
- [x] Term: **Intent** = one human wish, any roughness, spanning 1..n sprints. Replaces `request.md`
- [x] Layout (no spaces in paths; numeric prefix = historical order; intents 3 digits, sprints 2):
  ```
  docs/intents/001-user-authentication/
    intent.md       human, verbatim, immutable
    research.md     agent layer
    decisions.md    human layer
    backlog.md      human layer
    sprints/01-sign-in-email-password/
      brief.md      human layer
      ...           agent layer
  ```
  Human layer = intent, decisions, backlog, current brief. Each ≤50 lines
- [x] All intent files carry YAML frontmatter: author, owner, created, updated, stage. No version (git)
- [x] Ship runs deterministic gates (lint, tests) before push; Verify = LLM judge on the PR/MR only

## 3. Sprint run (autonomous) `[x]`
- [x] Stages: Research → Plan → Implement → Verify → Ship. No human until PR
- [x] Two research levels: intent research = options/landscape (feeds decisions); sprint research = implementation facts + interfaces (feeds plan). Sprint research reads intent research first
- [x] Agent-layer docs = handoff contracts, not transcripts
  - Only files that cross a fresh-context boundary or are needed to resume: `research.md`, `plan.md`, `progress.md`
  - Sub-agent I/O (prompt in, return out) is never written down
  - One consumer per doc; reference (`path:line`), don't copy code
  - Soft caps: research ≤100, plan ≤60 lines. Exceeding = sprint too big → report, don't write more
- [x] `plan.md`: work items, order, deliverable + test per item. No implementation detail (paths, methods, names) — implementer's call per project conventions
  - **Exception — interfaces**: parallelism preferred, so every contract between work items (A produces, B consumes) is worked out in sprint research and fixed in `plan.md` *before* implementation. Main agent passes only the needed bits to each sub-agent
- [x] `progress.md`: single durable sprint state. Items with done/undone + ≤1-line note each (result, issues: "sub-agent improvised", "took too long", "missed goal"). Git is the detailed trace: commit per item
- [x] Verify: separate verifier agent (fresh context) checks brief's acceptance criteria against diff + test/lint results, reports pass/fail per criterion. Implementer self-check only for lint/format/quality tools. Max 2 fix rounds, then ship as draft PR listing failed criteria
- [x] Ship
  - Branch `sprint/<intent>-<sprint>-<slug>`; commit per work item, conventional commits (`feat:`, `fix:`, `test:`)
  - PR/MR via `gh`/`glab`; body ≤20 lines: outcome, criteria with ✅/❌, assumptions, link to brief
  - Verifier posts result as PR/MR review: approve, or request-changes with per-criterion notes. Note: platforms block self-approval → needs separate account for verifier, else falls back to comment
  - Merge = human only

## 4. Documentation `[x]`
- [x] Artifacts + location: see layout in topic 2
- [x] Product-visible contracts (UI/UX, API structure, wording, business model, data model) = decisions
  - `decisions.md` = index, one line per D-number, ≤50 lines
  - `decisions/Dn-<slug>.md` = attachment per decision (ASCII wireframe, entity list, …), human-approved, no cap
  - `brief.md` references decisions (`← D4`), never repeats them
- [x] Lifecycle: keep everything; deletion is human, out of scope
  - Read scope: sprint agents read own sprint + intent-level docs only, never past sprints. Source of truth = code + module READMEs. Only intent research may read past intents/sprints

## 5. Project-level context `[x]`
- [x] `AGENTS.md` at root (≤60 lines, `CLAUDE.md` = `@AGENTS.md`): role, where things are, how to run tests. Human-maintained; agents propose edits via PR
- [x] `docs/architecture.md`: current state, not history. Facts only, no prose: `What → what for` (omit if obvious). Sections: Tech stack, Structure, Conventions, Infrastructure. Updated by sprints when they change it (DoD item), human approves via PR
- [x] Module READMEs: source of truth per module; implementer updates README of touched modules (DoD item)
- [x] No ADR¹ log — git history suffices

## 6. Quality `[x]`
- [x] Verify gates, cheap + deterministic first, **break on first failure** → back to agentic team:
  1. Lint/format (project tools)
  2. Tests: new behaviour covered, full suite green
  - `qa` agent writes acceptance tests (from brief, black-box, parallel); implementer writes unit tests. Rules: every test names its reason; red first; implementer never edits qa tests; verifier asks "would it fail if the feature were removed?"
  3. Verifier (LLM judge): acceptance criteria pass; no secrets/debug leftovers; module README + `architecture.md` updated if touched; `progress.md` complete; PR body written

## 7. Shipping `[x]`
- [x] See topic 3 → Ship

## 8. Tooling `[x]`
- [x] Claude Code first (`.claude/agents`, `.claude/skills`, `Agent` tool for sub-agents). Instructions = harness-neutral markdown → porting to Codex/Gemini CLI is a file-move, not a rewrite
- [x] MCP: **context7** for documentation, **playwright** for the browser. Both recommended, neither required — each has a documented fallback (`docs-lookup`, `browser-check`)
  - Agents name them by keyword only, never by tool id: the same server appears as `mcp__<server>__*` or `mcp__plugin_<plugin>_<server>__*` depending on the install form
  - Consequence: agents use `disallowedTools` instead of `tools`, because a `tools` allowlist drops every MCP tool that is not named explicitly (see `stack.md → Tool access`)
  - Docs rule: mandatory lookup before any framework API the repository does not already demonstrate; always pinned to the installed version, never the latest release

---
¹ ADR = Architecture Decision Record: short doc capturing one decision, its context and consequences.

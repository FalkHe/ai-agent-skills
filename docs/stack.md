# Agent Stack

Derived from `workflow.md`. Agreed 2026-09-14. Files: `agents/*.md`, `skills/*/SKILL.md`.

## Command skills (human-invoked, `disable-model-invocation: true`, run in main context)

| Skill | Phase | Does |
|---|---|---|
| `/fhit:intent <text>` | 1 | Interactive. Creates `docs/intents/NNN-<slug>/`, captures verbatim, research via `architect`, roasts with the human, records decisions → human approves |
| `/fhit:backlog <intent>` | 1 | Slices approved decisions into sprint outcomes, drafts briefs → human approves (last gate before PR) |
| `/fhit:issue <#id>` | 1–2 | Same flow, human in the loop via issue comments (proposals → `approve` / `Dn:` replies). Then backlog + sprints |
| `/fhit:refine <intent>` | 1/2 | Add/change decisions (+ attachments), re-check backlog for conflicts |
| `/fhit:sprint <intent> [NN]` | 2 | Autonomous, no questions: research → plan → implement ‖ qa → gates → ship → verify. No NN = all open sprints |
| `/fhit:init` | 0 | Scaffolds `AGENTS.md`, `CLAUDE.md` import, `docs/architecture.md`, `docs/intents/` |
| `/fhit:status <intent \| sprint>` | – | Reads state (`backlog.md`, `progress.md`); resume point after crash |

Plan and Ship stay in the main agent (`/fhit:sprint`): plan needs full sprint context, ship is deterministic.

## Agents (sub-agents, fresh context, one profession each)

| Agent | Phase | Profession |
|---|---|---|
| `architect` | 1, 2 | option research, data model, API structure, `architecture.md`; reviews `plan.md` for interface gaps |
| `ux-designer` | 1 | flows, wireframes (ASCII), wording → decision attachments |
| `ui-designer` | 1, 2 | visual system: colors, typography, spacing, components → decision attachments; implements styling in sprints |
| `backend-php` | 2 | Laravel |
| `backend-python` | 2 | FastAPI, Typer |
| `frontend` | 2 | React/Next, Vue, Tailwind, MUI |
| `qa` | 2 | acceptance tests from brief + interface contracts, black-box, parallel to implementation |
| `verifier` | 2 | LLM judge on PR/MR; checks tests for reason + real assertions |

Main agent routes work items by type. Each specialist researches its own area in Phase 2.

## Knowledge skills (`user-invocable: false`; loaded by Claude when relevant or preloaded into agents via `skills:`)

| Skill | Content | Preloaded into |
|---|---|---|
| `workflow` | layout, frontmatter, caps, handoff format, templates | all agents, all command skills |
| `git-flow` | branches, conventional commits, `gh` / `glab` PR + review commands | `/fhit:sprint`, `verifier` |
| `laravel` | conventions, testing, best practices | `backend-php` |
| `fastapi`, `typer` | same | `backend-python` |
| `react-next`, `vue` | same | `frontend` |
| `tailwind`, `mui` | tokens, styling rules | `ui-designer` |
| `docker-dev` | local services (MariaDB, PostgreSQL, Redis) | on demand |

Terminology (Claude Code, 2026-09): `.claude/commands/` is deprecated; both kinds are **skills** in `.claude/skills/<name>/SKILL.md`. Agents live in `.claude/agents/<name>.md`; body = system prompt.

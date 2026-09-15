# Agent Stack

Derived from `workflow.md`. Agreed 2026-09-14. Files: `plugins/fhit/agents/*.md`, `plugins/fhit/skills/*/SKILL.md`.

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
| `docs-lookup` | version-correct docs: context7 → local → official web, version verified | `architect`, `backend-php`, `backend-python`, `frontend`, `ui-designer`, `qa`, `verifier` |
| `browser-check` | Playwright test runner for committed e2e tests; playwright browser tools for live visual checks | `frontend`, `ui-designer`, `ux-designer`, `qa`, `verifier` |

## Tool access

Agents use `disallowedTools` (denylist), not `tools` (allowlist). A `tools` allowlist removes **every** MCP tool unless each server is named, and the context7 / playwright servers appear under different names depending on how the user installed them (`mcp__context7__*` when added directly, `mcp__plugin_<plugin>_<server>__*` when added as a plugin). The denylist keeps the agent's MCP access working whatever the installation, at the price of a wider built-in tool pool. `Agent` is denied everywhere so specialists cannot spawn specialists; write tools are denied for the read-only agents (`architect` keeps `Write` for its research file, `ux-designer` for its attachment).

Instructions name the servers only by the keywords **context7** and **playwright**, never by tool id — the agent picks whichever variant its session offers.

Terminology (Claude Code, 2026-09): `.claude/commands/` is deprecated; both kinds are **skills** in `.claude/skills/<name>/SKILL.md`. Agents live in `.claude/agents/<name>.md`; body = system prompt.

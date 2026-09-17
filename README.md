# ai-agent-skills

An agent stack for software development, shipped as the Claude Code plugin **`fhit`**. It turns a rough human wish into merged pull requests: the human decides everything a product user would notice, the agents do the rest — research, slicing, implementation, tests, PR, review.

> **Status: work in progress.** The workflow is agreed and the plugin installs, but it is under active development and not yet proven across many projects. Expect breaking changes.

## The workflow

Work is split into two phases — one with the human in the loop, one without.

**Phase 1 — Intent (human in the loop).** An *intent* is one wish in any roughness ("we need user auth"). The stack researches options and the existing codebase, then *roasts* the human: it asks only product-visible questions and records each answer as a one-sentence **decision** — fixed, changeable only by a human. Anything a user would not notice, the agents conclude themselves and list for veto. Once decisions are approved, the intent is sliced into a **backlog** of sprint outcomes, which the human approves. That is the last gate.

**Phase 2 — Sprint (autonomous).** A *sprint* is one outcome verifiable by using the product in under ten minutes, with zero open decisions. Each sprint runs unattended: research → plan → parallel implementation by work item → acceptance tests written blind from the brief → lint and full suite green → pull request written for the product owner → a fresh-context verifier judging the PR against its brief. **Merging is the human's job** and nothing else.

Everything lives in `docs/intents/` as the source of truth and is mirrored into the tracker: milestone per intent, issue per sprint, PR/MR per sprint. Human-facing documents are capped at 500 words, agent-to-agent handoffs at 1000 — a plan that outgrows its cap means the sprint is too big.

Full reference: [`docs/workflow.md`](docs/workflow.md).

## Commands

| Command | Does |
|---|---|
| `/fhit:init` | Scaffolds a project for the workflow — `AGENTS.md`, `CLAUDE.md` import, `docs/architecture.md`, `docs/intents/`. Idempotent |
| `/fhit:intent` | Starts an intent from chat: captures the wish, researches, discusses until decisions are approved |
| `/fhit:issue` | Same, driven from a GitHub/GitLab issue — gates answered as issue comments (`approve`, `Dn: …`, `stop`) |
| `/fhit:backlog` | Slices an approved intent into sprint outcomes and drafts one brief each, for human approval |
| `/fhit:refine` | Adds or revises a decision mid-way and re-checks the backlog for conflicts |
| `/fhit:sprint` | Runs one or all open sprints autonomously through to a verified PR. Asks nothing |
| `/fhit:status` | Shows intent/sprint state and the resume point after an interruption. Read-only |

## Agents

Each runs in its own context and gets only what its job needs.

| Agent | Provides |
|---|---|
| `architect` | Option research, codebase facts, data models, API structure, work-item slicing, interface contracts. Read-only |
| `ux-designer` | User flows, screen structure and wording as ASCII wireframes — proposals attached to decisions. No styling, no code |
| `ui-designer` | The visual system — colors, typography, spacing, component styling in Tailwind or MUI |
| `backend-php` | One backend work item in PHP/Laravel: models, migrations, controllers, jobs, tests |
| `backend-python` | One backend work item in Python: FastAPI endpoints, Typer commands, services, tests |
| `frontend` | One frontend work item in TypeScript: React/Next.js or Vue components, state, API calls, tests |
| `qa` | Black-box acceptance tests, one per criterion, derived from the brief without reading implementation code |
| `verifier` | Fresh-context judgement of the finished PR against brief and decisions — approve or request changes |

## Skills

**Process** — `workflow` (document layout, frontmatter, word caps, handoff format; loaded by every agent), `git-flow` (branch, commit, milestone/issue, PR/MR conventions with the exact `gh`/`glab` commands).

**Framework knowledge** — `laravel`, `fastapi`, `typer`, `react-next`, `vue`, `tailwind`, `mui`, `docker-dev`. Conventions, test runners and linters per stack, so implementers do not reinvent project structure.

**Tooling** — `docs-lookup` (get version-correct API docs: context7 → local sources → web search, always pinned to the installed version), `browser-check` (Playwright end-to-end tests and live visual observation of rendered UI).

Framework and tooling skills are preloaded into the agents that need them; users invoke only the commands above.

## Install

```
/plugin marketplace add FalkHe/ai-agent-skills
/plugin install fhit@fhit --scope project     # writes .claude/settings.json → teammates get it on clone
/fhit:init                                     # scaffold AGENTS.md, docs/architecture.md, docs/intents/
```

User-wide instead: drop `--scope project`. Update: `/plugin update fhit@fhit` (or `/plugin marketplace update fhit`).
Dev: `claude --plugin-dir /path/to/ai-agent-skills/plugins/fhit`.

### Recommended MCP servers

| Server | Used for | Without it |
|---|---|---|
| context7 | current docs for every framework API, checked against the installed version | agents fall back to installed source, then official docs via web search |
| playwright | browser acceptance tests and visual verification of rendered UI | no live browser check; UI criteria are judged from code only |

Any install form works (`claude mcp add …` or as a plugin) — the agents address them by keyword, not by tool name.

## Scope

Primary target is web applications with a dedicated frontend and backend.

Languages: PHP, Python, JavaScript/TypeScript, bash/ash/sh, HTML/CSS.
Frameworks: Laravel (full stack), FastAPI, Typer, React/Next.js, MUI, Vue.js, Tailwind.
Services and tools: Docker, MariaDB, PostgreSQL, Redis, `gh` CLI (GitHub), `glab` CLI (GitLab).

This is a first shortlist and may be extended. Details in [`docs/stack.md`](docs/stack.md).

## Repository layout

```
.claude-plugin/marketplace.json   marketplace index → ./plugins/<name>
plugins/fhit/                     the shipped plugin: .claude-plugin/plugin.json, agents/, skills/
docs/                             design notes, not shipped
```

# ai-agent-skills

This Project is for generating AI Agents and Skills for Development. Goal is to have a autonomous Agent Stack that can develop software mostly on it's own.

## Software Scope

Primary software scope is web applications with dedicated frontend and backend.

## Stack Scope

(first shout, might be extended)

We might deal with languages like

- PHP
- Python
- Javascript/Typescript
- bash/ash/sh
- HTML/CSS

Prefered Frameworks are
- Laravel (full stack)
- FastAPI
- Typer
- React / Next.js
- MUI
- Vue.js
- Tailwind

Prefered services/tools
- Docker
- MariaDB
- PostgreSQL
- Redis
- gh CLI (GitHub)
- glab CLI (GitLab)

## Install (Claude Code plugin `fhit`)

```
/plugin marketplace add FalkHe/ai-agent-skills
/plugin install fhit@fhit --scope project     # writes .claude/settings.json → teammates get it on clone
/fhit:init                                     # scaffold AGENTS.md, docs/architecture.md, docs/intents/
```
User-wide instead: drop `--scope project`. Update: `/plugin update fhit@fhit` (or `/plugin marketplace update fhit`).
Dev: `claude --plugin-dir /path/to/ai-agent-skills/plugins/fhit`.

Commands: `/fhit:intent`, `/fhit:backlog`, `/fhit:sprint`, `/fhit:refine`, `/fhit:status`, `/fhit:issue`. Docs: `docs/workflow.md`, `docs/stack.md`.

## Repository layout

```
.claude-plugin/marketplace.json   marketplace index → ./plugins/<name>
plugins/fhit/                     the shipped plugin: .claude-plugin/plugin.json, agents/, skills/
docs/                             design notes, not shipped
```

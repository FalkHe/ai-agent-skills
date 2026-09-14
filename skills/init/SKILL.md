---
name: init
description: Scaffolds a project for the fhit workflow — AGENTS.md, CLAUDE.md import, docs/architecture.md, docs/intents/. Idempotent; never overwrites existing content.
disable-model-invocation: true
---

# Init

1. Detect stack: lockfiles, `composer.json`, `pyproject.toml`, `package.json`, `docker-compose*.yml`. Read existing `README.md`, `CLAUDE.md`, `AGENTS.md`.
2. `AGENTS.md`: missing → from `workflow` skill template `templates/AGENTS.md`, commands filled from detected tooling; unknown → `<TODO>`. Exists → add only the `## Workflow` section if absent.
3. `CLAUDE.md`: missing → single line `@AGENTS.md`. Exists without it → append the line.
4. `docs/architecture.md`: missing → template, tech stack filled from step 1; rest `<TODO>`.
5. `docs/intents/.gitkeep`.
6. Report ≤10 lines: files created/changed, `<TODO>` count, next: `/fhit:intent <wish>`.

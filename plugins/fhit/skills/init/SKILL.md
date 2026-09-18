---
name: init
description: Scaffolds a project for the fhit workflow — AGENTS.md (incl. repository provider and accounts), CLAUDE.md import, docs/architecture.md, docs/intents/. Idempotent; never overwrites existing content.
disable-model-invocation: true
---

# Init

1. Detect stack: lockfiles, `composer.json`, `pyproject.toml`, `package.json`, `docker-compose*.yml`. Read existing `README.md`, `CLAUDE.md`, `AGENTS.md`.
2. `AGENTS.md`: missing → from `workflow` skill template `templates/AGENTS.md`, commands filled from detected tooling; unknown → `<TODO>`. Exists → add only the `## Workflow` section if absent.
3. `CLAUDE.md`: missing → single line `@AGENTS.md`. Exists without it → append the line.
4. `docs/architecture.md`: missing → template, tech stack filled from step 1; rest `<TODO>`.
5. `docs/intents/.gitkeep`.
6. Provider (`git-flow → Setup`). Propose from `git remote get-url origin`: host `github.com` → `github`, `gitlab.com` → `gitlab`, any other host or no remote → ask (self-hosted GitHub/GitLab, or `none`). The human confirms every value; never write an unconfirmed one.
   Provider ≠ `none` → `gh auth status` / `glab auth status`; the signed-in account → propose as `Agent`. Ask for `Human`: the product owner's account, who reviews and merges. CLI missing or signed out → write the provider anyway, `<TODO>` with the login command.
   Write the three lines into `AGENTS.md → Workflow`; lines already present → show, confirm, update.
7. Report what is set up now, any `<TODO>` left for the human, next: `/fhit:intent <wish>`.

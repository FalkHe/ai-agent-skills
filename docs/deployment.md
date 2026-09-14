# Deployment

Researched 2026-09-14. How target projects get the agents and skills.

## What each harness reads

| | Skills | Sub-agents | Instructions file |
|---|---|---|---|
| **Claude Code** | `.claude/skills/*/SKILL.md`; plugin = folder with `.claude-plugin/plugin.json` + `skills/` + `agents/`. Auto-loads from `~/.claude/skills/<plugin>/` or `<project>/.claude/skills/<plugin>/` — no install step | `.claude/agents/*.md` (YAML frontmatter + markdown) | `CLAUDE.md` (`@AGENTS.md` import) |
| **Codex** | `.agents/skills/` (repo, `~`, `/etc/codex`). Open Agent Skills format | `.codex/agents/*.toml` (`name`, `description`, `developer_instructions`) | `AGENTS.md` |
| **Gemini CLI** | `.agents/skills/` or `.gemini/skills/`; `gemini skills install <git-url> [--scope workspace]` | separate feature, own format | `GEMINI.md` (or `AGENTS.md` via `context.fileName`) |
| **Junie** | `.agents/skills/` or `.junie/skills/`; `~/.junie/skills/`. Open format. Install = `git clone` into the dir | none documented | `.junie/guidelines.md` |

Common denominator: **`.agents/skills/<name>/SKILL.md`** is read by Codex, Gemini, Junie (Claude Code does not scan it). Sub-agents are Claude-Code-only in our format; Codex needs TOML, Gemini/Junie have no equivalent → on those harnesses command skills must inline the roles.

## Options

| Option | Install (per project) | Claude | Codex/Gemini/Junie | Updates | Effort to build |
|---|---|---|---|---|---|
| A. **Skills-dir plugin**: this repo is a plugin; `git clone` into `~/.claude/skills/` | once, global | full (agents + namespaced skills) | none | `git pull` + `/reload-plugins` | plugin.json only |
| B. **Marketplace**: `marketplace.json` in this repo; `/plugin marketplace add FalkHe/ai-agent-skills` + `/plugin install` | 2 commands, user or project scope | full | none | `/plugin update`, version-gated | plugin.json + marketplace.json |
| C. **Cross-harness layout**: repo laid out as `.agents/skills/` + `.claude/agents/`; project adds it as git submodule / subtree, plus symlinks `.claude/skills → .agents/skills` | submodule + 2 symlinks | full | skills yes, agents no | `git submodule update` | restructure + install script |
| D. **Copy script** `install.sh <project>` | one command | full | skills yes | re-run script | script |

## Decision (2026-09-14)

Plugin `fhit`, marketplace in this repo (`.claude-plugin/marketplace.json`, source `./`). Install per project with `--scope project` → `.claude/settings.json` carries `"fhit@fhit": true`; Claude Code fetches the plugin, nothing else enters the project repo. Update: `/plugin update fhit@fhit`; `version` in plugin.json is the update gate → bump it (and tag) per release. `/fhit:init` scaffolds `AGENTS.md`, `docs/architecture.md`, `docs/intents/`.

## Options considered

**A now, B later, C only if Codex/Junie become real.**

- A is zero-install: clone once, every project on the machine has `/ai-agent-skills:intent` etc. and all agents. Matches "least effort, highest convenience". Project-local install (`<project>/.claude/skills/`) works the same way when a team needs it pinned.
- B costs one extra file and gives teammates `/plugin install`; add when sharing.
- Codex/Gemini/Junie: the agents (the core of this stack) have no home there. Porting = rewriting command skills to inline roles + TOML agents for Codex. Defer until needed; keep skill bodies harness-neutral so the day it happens is a layout change.

Namespacing note: plugin skills are invoked as `/<plugin-name>:intent`. Short plugin name recommended (`fh`) → `/fh:intent`, `/fh:sprint`.

## Target project needs (not delivered by the plugin)

- `AGENTS.md` (+ `CLAUDE.md` = `@AGENTS.md`): test/lint commands, structure, quirks
- `docs/architecture.md`
- `docs/intents/` is created by `/intent`
→ provide as templates in the `workflow` skill, plus a `/fh:init` command skill that scaffolds them.

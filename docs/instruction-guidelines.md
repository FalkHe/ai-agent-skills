# Instruction Guidelines

Distilled from Anthropic (Claude Code + Agent Skills), OpenAI Codex (AGENTS.md), Google Gemini CLI docs. Checked 2026-09-14.

## Universal

- Only add what the model doesn't already know. Per line: "would removing this cause a mistake?" No → cut
- Bloat makes rules get lost. Emphasis (`IMPORTANT`) on one line only, never many
- Imperative, third person in descriptions ("Reviews code…", not "I can…")
- One term per concept, used consistently
- Provide a default, not a menu of options
- No time-sensitive statements
- Give the agent a check it can run (tests, lint, script). Evidence over assertion
- Fresh-context reviewer for grading; tell it to report only gaps affecting correctness or stated requirements

## Claude Code specifics

**Skills** `.claude/skills/<name>/SKILL.md`
- `name` ≤64 chars, lowercase+hyphens; `description` ≤1024 chars = what + when (drives auto-selection)
- Body <500 lines; progressive disclosure: SKILL.md = overview, details in sibling files, references one level deep
- `disable-model-invocation: true` → human-triggered workflow. `user-invocable: false` → knowledge only
- `$ARGUMENTS` for input. `context: fork` runs in isolation
- Degrees of freedom: fragile/sequential → exact commands; judgment tasks → heuristics
- Checklists for multi-step workflows; validator → fix → repeat loops

**Subagents** `.claude/agents/<name>.md`
- Body = system prompt. `description` short (all descriptions together <15k tokens)
- `tools`, `model`, `skills` (preload), `maxTurns`, `memory`, `isolation: worktree`
- Startup context: system prompt + task message + CLAUDE.md + preloaded skills. No conversation history

**CLAUDE.md / AGENTS.md**
- Include: commands it can't guess, style deviating from defaults, test runner, repo etiquette, gotchas
- Exclude: anything derivable from code, standard conventions, tutorials, file-by-file descriptions
- `@path` imports

## Codex (AGENTS.md)

- 32 KiB limit. Root → subdir chain, later overrides earlier. `AGENTS.override.md` for nested rules
- Exclude what linters/CI already enforce

## Gemini CLI (GEMINI.md)

- Global → workspace → just-in-time. `@file.md` imports. `AGENTS.md` usable via `context.fileName` setting

## Portability

AGENTS.md is read by Codex, Gemini (configured), Cursor, Copilot, 25+ tools. Plain markdown body of skills/agents is portable; only frontmatter differs.

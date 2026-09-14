---
name: workflow
description: Rules for the intent → sprint development workflow shared by all agents. Defines document layout under docs/intents/, frontmatter, size caps, human vs. agent layer, handoff format. Preloaded into every agent and command skill; not invoked by users.
user-invocable: false
---

# Workflow

Templates: `templates/`.

## Terms

- **Intent**: one human wish, spans 1..n sprints. `docs/intents/NNN-<slug>/`
- **Sprint**: one outcome, one autonomous run. `sprints/NN-<slug>/`
- **Decision** `Dn`: human-fixed. Never change; propose changes to the human
- **Conclusion**: agent-derived. Cite source (`← D3`) or mark `ASSUMPTION`

## Layers

| Layer | Files | Cap | Who reads |
|---|---|---|---|
| Human | `intent.md`, `decisions.md`, `decisions/*.md`, `backlog.md`, `brief.md` | ≤50 lines (brief ≤30; attachments uncapped) | human + agents |
| Agent | `research.md`, `plan.md`, `progress.md` | research ≤100, plan ≤60 | next agent only |

Exceeding a cap = scope too big. Stop, report, don't write more.

## Rules

1. Product user would notice the difference → human decision. Else → agent's call, listed under `Assumptions` for veto.
2. Docs are handoff contracts. Write only what the next reader needs to act. Point to code (`path:line`), don't paste it.
3. Sub-agent input = prompt, output = return value. Never write these to files.
4. Source of truth = code + module `README.md`. Never read past sprints. Intent research may read past intents.
5. Interfaces between parallel work items are fixed in `plan.md` before implementation.
6. `progress.md` is the only sprint state. Update after every work item: status + ≤1 line.
7. Mid-sprint discovery → line in `progress.md` under `Backlog proposals`. Never grow the sprint.

## Frontmatter

Every file under `docs/intents/`:

```yaml
---
author: <human> | intake | sprint | qa | verifier | <agent-name>
owner: human | agent
created: YYYY-MM-DD
updated: YYYY-MM-DD
stage: draft | approved | done      # omit where meaningless
---
```

`<human>` = `git config user.name <user.email>` unless the human states otherwise.
`owner: human` files: agents create drafts, never modify after `stage: approved`.

## Handoff format (sub-agent return value)

```
RESULT: done | partial | blocked
<≤5 lines: what was done, what the caller must know>
FILES: <paths touched, if any>
```

## Templates

`templates/AGENTS.md`, `architecture.md` (project scaffold); `intent.md`, `decisions.md`, `backlog.md`, `brief.md`, `research.md`, `plan.md`, `progress.md`. Copy, fill, delete unused sections.

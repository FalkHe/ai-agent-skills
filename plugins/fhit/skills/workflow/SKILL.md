---
name: workflow
description: Rules for the intent → sprint development workflow shared by all agents. Defines document layout under docs/intents/, frontmatter, who the human reader is, word limits, what to report, handoff format. Preloaded into every agent and command skill; not invoked by users.
user-invocable: false
---

# Workflow

Templates: `templates/`.

## Glossary

- **Intent**: one human wish, spans 1..n sprints. `docs/intents/NNN-<slug>/`
- **Task**: the product work to review, build, or change. A Task may be decomposed into agent-layer work items or subtasks during planning
- **Sprint**: one Task, or a coherent group of subtasks that completes it, delivered in one autonomous run. `sprints/NN-<slug>/`
- **Backlog**: the list of Sprints/Tasks belonging to one Intent, stored in `backlog.md`
- **Outcome**: the true/false statement a PM/PO can verify by using the product or reviewing the merge request in ≤5 min to confirm that the Sprint's Task is complete
- **Review**: the sprint branch plus `review.md`, read by the human before merging; with a provider¹ also the PR/MR
- **Decision** `Dn`: human-fixed. Never change; propose changes to the human
- **Conclusion**: agent-derived. Cite source (`← D3`) or mark `ASSUMPTION`

¹ Provider = GitHub or GitLab, chosen per project in `AGENTS.md → Workflow` (`git-flow → Setup`); `none` is a full mode, not a degraded one.

## Layers

| Layer | Files | Who reads |
|---|---|---|
| Human | `intent.md`, `decisions.md`, `decisions/*.md`, `backlog.md`, `brief.md`, `review.md` | the product owner |
| Agent | `research.md`, `plan.md`, `progress.md` | the next agent only |

## Writing for the human

The reader of everything a human sees — the files above, issue comments, chat reports — is the product owner: knows the product and its users, has not read the code. Write what changes for someone using the product. No file paths, symbols, framework or library names, no commands. An internal id (`D3`, `AC2`, `WI1`) may trail a sentence, never open one. A sentence that needs knowledge of the code to parse belongs in the agent layer.

## Length

Count words, never lines (`wc -w`); anything shorter is counted in sentences. Lines are not a unit — a wall of text on one line breaks the rule it appears to keep.

| | best | max |
|---|---|---|
| Human document (`intent`, `decisions`, `backlog`, `brief`) | 250 | 500 |
| Human message (`review.md`, issue comment, chat report) | 100 | 250 |
| `research.md` | 500 | 1000 |
| `plan.md` | 250 | 500 |

Attachments under `decisions/` are uncapped. Over the max means the scope is too big, not the text: stop, report, don't write more.

## Reporting

Report exceptions, not progress. Default = one sentence of outcome plus the link. Add a sentence only for what the human must act on or would be surprised by: a failed criterion, an assumption you made, a scope cut, a proposal. Nothing went wrong → the outcome sentence alone. Never list steps performed, counts of files, tests or agents, or which stages ran.

Name a failure by what a user would notice ("signing in with a wrong password shows no error"), never by where it broke. Evidence — `path:line`, test name, command output — belongs in the return value and `progress.md`, never in a posted comment.

## Rules

1. Product user would notice the difference → human decision. Else → agent's call, listed under `Assumptions` for veto.
2. Docs are handoff contracts. Write only what the next reader needs to act. Point to code (`path:line`), don't paste it.
3. Sub-agent input = prompt, output = return value. Never write these to files.
4. Source of truth = code + module `README.md`. Never read past sprints. Intent research may read past intents.
5. Interfaces between parallel work items are fixed in `plan.md` before implementation.
6. `progress.md` is the only sprint state. Update after every work item: status + one sentence.
7. Mid-sprint discovery → line in `progress.md` under `Backlog proposals`. Never grow the sprint.
8. Files and git are the source of truth and work alone. A provider mirrors them: intent = milestone, backlog Sprint = issue, sprint = PR/MR with `review.md` as body. Steps and their plain-git defaults in `git-flow → Provider steps`.
9. `backlog.md` is a short PM/PO overview table: one Task sentence per Sprint plus dependency, issue, and status. `brief.md` is the complete Sprint contract; `## Task` is always its first `##` section.

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
<what was done and what the caller must know, ≤3 sentences>
FILES: <paths touched, if any>
```

## Templates

`templates/AGENTS.md`, `architecture.md` (project scaffold); `intent.md`, `decisions.md`, `backlog.md`, `brief.md`, `research.md`, `plan.md`, `progress.md`, `review.md`. Copy, fill, delete unused sections.

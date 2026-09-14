---
name: issue
description: Runs an intent from a GitHub or GitLab issue with the human in the loop via issue comments — research, proposed decisions, approval, backlog, sprints. Use when the wish arrives as an issue.
disable-model-invocation: true
argument-hint: <issue number>
---

# Issue $ARGUMENTS

Same flow as `/intent` → `/backlog` → `/sprint`, but the conversation is the issue thread. Platform commands per `git-flow`.

## 1. Capture
`gh issue view $ARGUMENTS --json title,body,comments` / `glab issue view`. `intent.md` = title + body verbatim, author = issue author, `source: <issue url>`.

## 2. Research
`architect` intent mode → `research.md`.

## 3. Propose decisions
Draft `decisions.md` (`stage: draft`): every product-visible question with your proposed answer, closest to the issue text and existing product; attachments via designers/architect where needed.
Comment on the issue (≤30 lines): proposals as a numbered list, each "reply `Dn: <your answer>` to change", plus `approve` / `stop`.

## 4. Wait
Poll comments (`gh issue view --json comments`) every 5 min, up to 24 h. Apply `Dn:` replies to `decisions.md`, re-post changed lines. On `approve` → `stage: approved`, `approved-by: <commenter>`. `stop` or issue closed → halt. Timeout → comment, halt.

## 5. Backlog
Run `/backlog III`; post the backlog as a comment, wait for `approve` as in 4.

## 6. Sprints
`/sprint III` — all open sprints. Before each sprint re-read the issue; `stop`/closed → halt.

## 7. Report
Final comment: PR links, verdicts, failed items.

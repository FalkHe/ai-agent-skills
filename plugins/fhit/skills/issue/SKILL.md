---
name: issue
description: Runs an intent from a GitHub or GitLab issue with the human in the loop via issue comments — research, proposed decisions, approval, backlog, sprints. Use when the wish arrives as an issue. Needs a provider configured by /fhit:init.
disable-model-invocation: true
argument-hint: <issue number>
---

# Issue $ARGUMENTS

Same flow as `/fhit:intent` → `/fhit:backlog` → `/fhit:sprint`, but the conversation is the issue thread.

Pre-condition: `AGENTS.md → Workflow` names `Provider: github` or `gitlab` and both accounts (`git-flow → Setup`). `none` or missing → stop: this command needs a provider, point to `/fhit:intent` or `/fhit:init`. Load the provider skill; every command below is a section of it. Only comments by `Human` count as answers; comments by other accounts are ignored.

## 1. Capture
`Issue thread → read`. Resolve pointers in the body as in `/fhit:intent` step 1 (a reference to a file, path, or URL → read it, restate the wish from its content; never store the pointer as the wish). `intent.md` = title + body, self-contained parts verbatim, author = issue author, `source: <issue url>` + each resolved pointer.

`Intent approved` (milestone), then `Issue thread → milestone` on the source issue. The source issue stays the intent's issue; `/fhit:backlog` adds one per sprint under the same milestone.

## 2. Research
`architect` intent mode → `research.md`.

## 3. Propose decisions
Draft `decisions.md` (`stage: draft`): every product-visible question with your proposed answer, closest to the issue text and existing product; attachments via designers/architect where needed.
`Issue thread → comment`: proposals as a numbered list in the product owner's language, each "reply `Dn: <your answer>` to change", plus `approve` / `stop`.

## 4. Wait
`Issue thread → read` every 5 min, up to 24 h. Apply `Dn:` replies to `decisions.md`, re-post changed lines. On `approve` → `stage: approved`, `approved-by: <Human>`. `stop` or issue closed → halt. Timeout → comment, halt.

## 5. Backlog
Run `/fhit:backlog III`; post the backlog as a comment, wait for `approve` as in 4.

## 6. Sprints
`/fhit:sprint III` — all open sprints. Before each sprint re-read the issue; `stop`/closed → halt.

## 7. Report
Final comment: what the product can now do, with the PR/MR links. Name anything that failed as a user would notice it.

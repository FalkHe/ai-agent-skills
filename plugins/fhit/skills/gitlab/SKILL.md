---
name: gitlab
description: GitLab implementation of the git-flow provider steps — milestone, issue, merge request, review verdict, status and issue-thread commands with glab, including reviewer and assignee handling. Loaded when AGENTS.md → Workflow says Provider gitlab; not invoked by users.
user-invocable: false
---

# GitLab (`glab`)

Sections match `git-flow → Provider steps`; run each after its plain-git default. `<M>` = milestone title = intent directory name (`001-user-authentication`). `Human`, `Agent` from `AGENTS.md → Workflow`. `<f>` = a temp file with the body.

Issues, not work items: `glab work-items` is experimental and lacks `--milestone`; GitLab shows issues as work items anyway. Milestones via `glab api` — works on every `glab` version.

## Account
Once per run, before anything else: `glab auth status` succeeds and `glab api user --jq .username` equals `Agent`. Else stop and report which account is signed in.

## Intent approved
- find: `glab api "projects/:id/milestones?title=<M>" --jq '.[0].id'`
- empty → create: `glab api projects/:id/milestones -f title='<M>' -f description='<intent title> — docs/intents/<M>/'`
- `intent.md` `milestone:` = its `web_url`

## Backlog approved
Milestone as above. Per `open` row:
- no issue: `glab issue create -t "<task title>" --description-file <f> -m '<M>' -a <Agent>` → `Issue` column `#<n>`
- issue exists: `glab issue update <n> -t "<task title>" --description-file <f>`

## Sprint shipped
Body = `review.md` without frontmatter and title, plus a last line `Closes #<issue>` (the merge closes the issue; never close it by hand).
`glab mr create --title "<task title>" --description "$(cat <f>)" -m '<M>' --reviewer <Human> --assignee <Agent>`; any failed row → add `--draft`. `review.md` `url:` = the MR URL.
Re-ship in a later round: push only; the MR follows the branch.

## Review diff
`glab mr diff <n>`

## Verdict
- approve: `glab mr approve <n>` then `glab mr note <n> -m "<text>"`
- changes requested: `glab mr note <n> -m "<text>"` (GitLab has no request-changes state)
- approve refused (author = approver account, or approval rules): `glab mr note <n> -m "<text>"`, report blocked
- final round still failing: `glab mr update <n> --draft`

## Status
`glab mr view <n> --output json` (`state`, `draft`, `web_url`) · `glab issue view <n> --output json`

## Issue thread
- read: `glab issue view <n> --comments --output json` — an answer counts only when the note's `author.username` equals `Human`
- milestone: `glab issue update <n> -m '<M>'`
- comment: `glab issue note <n> -m "<text>"`

## Merge
Never `glab mr merge`, never `glab issue close`. Merging is the human's.

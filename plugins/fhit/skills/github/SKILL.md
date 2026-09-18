---
name: github
description: GitHub implementation of the git-flow provider steps — milestone, issue, pull request, review verdict, status and issue-thread commands with gh, including reviewer and assignee handling. Loaded when AGENTS.md → Workflow says Provider github; not invoked by users.
user-invocable: false
---

# GitHub (`gh`)

Sections match `git-flow → Provider steps`; run each after its plain-git default. `<M>` = milestone title = intent directory name (`001-user-authentication`). `Human`, `Agent` from `AGENTS.md → Workflow`. `<f>` = a temp file with the body.

## Account
Once per run, before anything else: `gh auth status` succeeds and `gh api user --jq .login` equals `Agent`. Else stop and report which account is signed in.

## Intent approved
- find: `gh api repos/{owner}/{repo}/milestones --jq '.[]|select(.title=="<M>").number'`
- empty → create: `gh api repos/{owner}/{repo}/milestones -f title='<M>' -f description='<intent title> — docs/intents/<M>/'`
- `intent.md` `milestone:` = its `html_url`

## Backlog approved
Milestone as above. Per `open` row:
- no issue: `gh issue create -t "<task title>" -F <f> -m '<M>' -a <Agent>` → `Issue` column `#<n>`
- issue exists: `gh issue edit <n> -t "<task title>" -F <f>`

## Sprint shipped
Body = `review.md` without frontmatter and title, plus a last line `Closes #<issue>` (the merge closes the issue; never close it by hand).
`gh pr create --title "<task title>" -F <f> -m '<M>' -r <Human> -a <Agent>`; any failed row → add `-d`. `review.md` `url:` = the PR URL.
Re-ship in a later round: push only; the PR follows the branch.

## Review diff
`gh pr diff <n>`

## Verdict
- approve: `gh pr review <n> --approve -b "<text>"`
- changes requested: `gh pr review <n> --request-changes -b "<text>"`
- refused (author = reviewer account, or policy): `gh pr comment <n> -b "<text>"`, report blocked
- final round still failing: `gh pr ready <n> --undo`

## Status
`gh pr view <n> --json state,isDraft,reviewDecision,url` · `gh issue view <n> --json state`

## Issue thread
- read: `gh issue view <n> --json title,body,author,state,comments` — an answer counts only when its `author.login` equals `Human`
- milestone: `gh issue edit <n> -m '<M>'`
- comment: `gh issue comment <n> -b "<text>"`

## Merge
Never `gh pr merge`, never `gh issue close`. Merging is the human's.

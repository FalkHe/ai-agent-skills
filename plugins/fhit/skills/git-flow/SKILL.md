---
name: git-flow
description: Branch, commit, tracker (milestone/issue), pull/merge request and review conventions, with the exact gh (GitHub) and glab (GitLab) commands. Preloaded into intent, backlog, sprint, verifier and issue agents; not invoked by users.
user-invocable: false
---

# Git flow

Detect platform once: `git remote get-url origin` → `github.com` → `gh`; else `glab`.

## Tracker
One milestone per intent, one issue per backlog line (= per sprint). Every sprint PR/MR carries the milestone and closes its issue.

Milestone title = the intent directory name, `<III>-<slug>` (e.g. `001-user-authentication`). Description = intent title + `docs/intents/<III>-<slug>/`.
Always look up before creating — the commands are re-run on every `/fhit:backlog`.

| | GitHub | GitLab |
|---|---|---|
| find | `gh api repos/{owner}/{repo}/milestones --jq '.[]\|select(.title=="<M>").number'` | `glab api "projects/:id/milestones?title=<M>" --jq '.[0].id'` |
| create | `gh api repos/{owner}/{repo}/milestones -f title='<M>' -f description='<d>'` | `glab api projects/:id/milestones -f title='<M>' -f description='<d>'` |
| issue | `gh issue create -t "<outcome>" -F <f> -m '<M>'` | `glab issue create -t "<outcome>" --description-file <f> -m '<M>'` |
| PR/MR | add `-m '<M>'` to create | add `-m '<M>'` to create |

`<M>` = milestone title; empty `find` result → create. Newer `glab` also has `glab milestone create`; the `api` form above works on every version.
Issues are closed by the merge (`Closes #<n>` in the PR/MR body). Never close one by hand.

## Branch
`sprint/<III>-<NN>-<slug>` from the default branch. Example: `sprint/001-02-sign-in-email-password`.

## Commits
Conventional: `<type>(<scope>): <summary>` — types `feat fix test refactor docs chore`. Scope = module or work item.
- One commit per work item minimum. Tests red before green: `test(auth): …` then `feat(auth): …`
- Never commit red. Lint + full test suite pass before every push
- Never `--force` on a shared branch, never `--no-verify`

## Pull / merge request
Push, then:

| | GitHub | GitLab |
|---|---|---|
| create | `gh pr create --title "<outcome>" --body-file <f>` | `glab mr create --title "<outcome>" --description "$(cat <f>)"` |
| draft | `gh pr ready --undo` | `glab mr update --draft` |
| diff | `gh pr diff <n>` | `glab mr diff <n>` |
| approve | `gh pr review <n> --approve -b "<text>"` | `glab mr approve <n>` + `glab mr note <n> -m "<text>"` |
| request changes | `gh pr review <n> --request-changes -b "<text>"` | `glab mr note <n> -m "<text>"` |
| comment | `gh pr comment <n> -b "<text>"` | `glab mr note <n> -m "<text>"` |
| issue comment | `gh issue comment <n> -b "<text>"` | `glab issue note <n> -m "<text>"` |

Self-approval is rejected when author = reviewer account → fall back to comment, state it.

Body — read by the product owner (`workflow`), so: what changed, how to see it, what to watch out for. No paths beyond the brief link, no symbols, no test names.
```
## What changed
<one or two sentences, in the product's terms>
## How to check it
- <criterion as something a user does and sees>
## Heads-up
<only if there is one: an assumption, a cut, a known gap — otherwise delete the section>

Brief: docs/intents/<III>-<slug>/sprints/<NN>-<slug>/brief.md
Closes #<sprint issue>
```
Milestone: `-m '<III>-<slug>'` on create (see Tracker).

Merge is human-only. Never merge, never close.

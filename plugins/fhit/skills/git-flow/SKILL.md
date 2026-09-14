---
name: git-flow
description: Branch, commit, pull/merge request and review conventions for sprints, with the exact gh (GitHub) and glab (GitLab) commands. Preloaded into sprint, verifier and issue agents; not invoked by users.
user-invocable: false
---

# Git flow

Detect platform once: `git remote get-url origin` → `github.com` → `gh`; else `glab`.

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

Body (≤20 lines):
```
## Outcome
<one line>
## Acceptance criteria
- AC1 … 
## Assumptions
- …
Brief: docs/intents/<III>-<slug>/sprints/<NN>-<slug>/brief.md
```

Merge is human-only. Never merge, never close.

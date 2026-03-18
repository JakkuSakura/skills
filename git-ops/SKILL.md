---
name: git-ops
description: Git operations for local repos and GitHub workflows. Use when Codex needs to inspect repo state, craft commits, manage branches, rebase/merge safely, or decide on Git/GitHub commands (including gh usage) while following safety constraints and commit conventions.
---

# Git Ops

## Overview

Handle routine Git operations with safety-first constraints, clear commit messaging, and minimal history risk.

## Workflow

1) Inspect repo state
- Use `git status -sb` and `git diff` to understand current changes.
- Avoid touching unrelated user changes; never revert changes you did not make.
- Ignore unexpected changes you did not make and do not include them in your commits.
- Assume other users or agents may be working concurrently; never interrupt, include, or alter their in-progress work unless explicitly instructed.

2) Choose the safest path
- Prefer non-destructive operations and reversible steps.
- Avoid `git reset --hard` or `git checkout --` unless explicitly requested.
- Do not amend commits unless explicitly requested.

3) Apply changes
- Stage only relevant files.
- If a command would rewrite history (rebase, amend, force push), require explicit user intent.
- For partial staging, `git add -p`.
- Otherwise, `git add` is fine for files you fully own in the current task.

4) Create commits
- Check commit message patterns in history first.
- If no pattern exists, use Conventional Commits.
```text
feat: A new feature
fix: A bug fix
docs: Documentation only changes
style: Changes that do not affect the meaning of the code (white-space, formatting, missing
chore: Changes to the build process or auxiliary tools and libraries such as documentation generation)
refactor: A code change that neither fixes a bug nor adds a feature
perf: A code change that improves performance
test: Adding missing tests or correcting existing tests

feat(certain-module): A new feature in a specific module
fix(certain-module): A bug fix in a specific module
...
```

5) GitHub interactions
- Prefer the `gh` CLI when interacting with GitHub.

## Commit Conventions

- Inspect prior commits to mirror existing style.
- Default to Conventional Commits when no clear pattern exists.

## Rebase/Merge Guidance

- Prefer fast-forward updates, then rebase, then merge (in that order).
- Do not recommend or execute history-rewriting commands unless explicitly requested.

## Destructive-Operation Gate

- Warn before destructive operations and provide safer alternatives.
- Treat deleting files/directories, rebuilding databases, `git reset --hard`, and `git push --force` as destructive.
- Confirm explicit intent before executing irreversible actions.

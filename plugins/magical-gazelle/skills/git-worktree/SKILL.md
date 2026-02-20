---
name: git-worktree
description: Manage Git worktrees for isolated parallel development using native git commands. Use when working on multiple features simultaneously or reviewing code in isolation.
---

# Git Worktree Manager

Manage parallel development with native `git worktree` commands. No wrapper scripts — just git.

## When to Use

- Working on two features that touch different parts of the same files
- Reviewing a PR while you have uncommitted work on another branch
- Running tests on one branch while developing on another

## When NOT to Use

- Single feature development — just use a branch
- Features that modify the same functions/lines — sequence them instead, conflicts are unavoidable

## Commands

### Create a worktree

```bash
# Create as a sibling directory (recommended)
git worktree add ../$(basename $(pwd))-feat-name -b feat/name main

# Example: if repo is "my-app", creates ../my-app-feat-login/
git worktree add ../my-app-feat-login -b feat/login main
```

Sibling directories are preferred over nested `.worktrees/` — your editor treats them as normal projects and there's no `.gitignore` management needed.

### List worktrees

```bash
git worktree list
```

### Remove a worktree

```bash
git worktree remove ../my-app-feat-login
```

### Prune stale worktrees

```bash
git worktree prune
```

## Workspace Scripts Convention

After creating a worktree, check for these two scripts in the project root:

- **`worktree-setup.sh`** — One-time environment setup (deps, `.env`, migrations, seed data). Should be idempotent.
- **`worktree-start.sh`** — Start the dev server, typically on a random port to avoid conflicts between worktrees.

```bash
git worktree add ../my-app-feat-login -b feat/login main
cd ../my-app-feat-login
test -f worktree-setup.sh && bash worktree-setup.sh
test -f worktree-start.sh && bash worktree-start.sh
```

If neither script exists, handle setup manually (copy `.env`, install deps, etc.).

## Workflow Integration

### With `/mg:work`

When starting feature work from the default branch, choose:

1. **Branch** (default): `git checkout -b feat/thing` — simplest, use for most work
2. **Worktree**: `git worktree add ../repo-feat-thing -b feat/thing main` — use when you want parallel development

After creating a worktree, check for `worktree-setup.sh` and run it. If not present, handle setup manually.

### With `/mg:review`

If already on the target branch, just review. If on a different branch with uncommitted work:

```bash
git worktree add ../repo-review-pr-123 -b review/pr-123 main
cd ../repo-review-pr-123
gh pr checkout 123
bash worktree-setup.sh  # if present
```

Clean up after review:

```bash
cd ../repo  # back to main checkout
git worktree remove ../repo-review-pr-123
```

## Merging Parallel Work

When both features are done:

```bash
# Merge the first feature
gh pr merge --squash

# Rebase the second feature onto updated main
cd ../my-app-feat-search
git fetch origin
git rebase origin/main
git push --force-with-lease
```

Conflicts from overlapping files will surface during the rebase. If both features added to the same files in different sections, these resolve trivially.

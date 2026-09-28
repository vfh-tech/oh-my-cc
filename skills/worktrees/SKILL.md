---
name: worktrees
description: Isolate feature work in a dedicated git worktree. Use when starting multi-file changes that should not disturb the main checkout, when running experiments, or when the user asks for worktree isolation.
---

# Worktrees

Isolate feature work in a dedicated git worktree.

## When
- Multi-file changes that need their own checkout
- Risky experiments parallel to ongoing work in the main checkout
- Running two agents on different features without write conflicts

## Steps

1. Verify clean state: `git -C <repo> status --porcelain`. Warn about uncommitted files in affected paths before proceeding.
2. Create: `git -C <repo> worktree add ../<repo>-<feature> -b <feature-branch>`
   - Branch name: kebab-case feature slug
   - If the branch exists, add it without `-b`
3. Confirm the worktree has the base the task needs: run the base test suite once if cheap, or `git log -1` check otherwise.
4. Report the worktree path and branch to the caller.

## Rules
- All work happens in the worktree path, never the main checkout
- Never delete a worktree with uncommitted changes; surface them first
- Remove finished worktrees only after their branch is merged or explicitly abandoned: `git worktree remove <path>`

## ponytail
Skip remote/worktree-locking coordination for shared checkouts. Add when multiple machines work the same repo.
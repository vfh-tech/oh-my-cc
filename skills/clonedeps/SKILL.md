---
name: clonedeps
description: Clone dependency source repositories read-only into .deps/repos/ for inspecting SDK, library, or framework internals. Use when documentation is insufficient and the actual upstream source is needed.
---

# Clonedeps

Clone dependency sources for internal inspection.

## Steps

1. Resolve each dependency to a pinned ref (tag, commit, or branch). Prefer the version actually installed; read the lockfile first.
2. Clone shallow into `.deps/repos/<owner>__<name>/`:
   `git clone --depth 1 https://github.com/<owner>/<name>.git .deps/repos/<owner>__<name>`
3. Pin the ref: `git -C .deps/repos/<owner>__<name> checkout <ref>` and record `ref=<ref>` in `.deps/repos/<owner>__<name>/.clonedeps-ref`.
4. If the repo exists, verify the ref matches; fetch and re-checkout only on mismatch.

## Rules
- `.deps/repos/` is READ-ONLY. Never edit files there. Inspection only
- Never commit `.deps/`; add it to `.gitignore`
- Prefer official mirrors. Report the exact ref used for every repo
- Large repos: `--depth 1` always; `--filter=blob:none` if history is needed

## Dispatch
- Discovery and ref resolution: librarian
- The clone commands themselves: orchestrator runs directly or via fixer
---
name: simplify
description: Behavior-preserving simplification pass over recently changed or specified code. Use after implementation, before commit, or when the user asks to simplify, deduplicate, or remove dead code.
---

# Simplify

Reduce code without changing behavior.

## Scope
Default: files changed in the working diff (`git status --porcelain` + `git diff --name-only`). The dispatch may name specific files instead.

## Pass checklist, in order
1. **Dead code**: unreachable branches, unused exports, unused deps, commented-out blocks. Delete.
2. **Duplication**: two implementations of the same logic. Merge to one. If the two differ in edge cases, keep the more correct one and note the difference.
3. **Unearned abstraction**: interface with one implementation, factory with one product, config for a value that never changes, wrapper that forwards 1:1. Inline it.
4. **Cleverness**: replace clever constructs with the boring obvious version.
5. **Overly defensive code**: catches that swallow errors, validation of internal-only data. Remove unless the boundary is external.

## Rules
- Behavior-preserving. Run the existing test suite before and after; if tests fail before the pass, stop and report
- No new dependencies, no new files unless a deletion moves code
- Public API and exports unchanged unless the caller is inside the same diff
- Report each change: file, what was removed/merged, why it is safe

## Output
Before/after line counts per file, list of changes, test result. If nothing qualified, say so in one line.
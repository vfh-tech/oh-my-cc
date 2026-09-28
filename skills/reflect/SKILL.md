---
name: reflect
description: Post-task retrospective. Use after completing a significant task or fixing a hard bug to capture lessons, slow paths, and recurring bug patterns into a dated retrospective file.
---

# Reflect

Run a retrospective after a significant task, a hard bug, or a multi-iteration loop.

## Steps

1. Gather the trail: what was attempted, what failed, what the actual fix was, time sinks.
2. Answer concretely:
   - What was slower than it needed to be, and what would have cut the delay?
   - What pattern caused the bug or the rework?
   - Which instruction, check, or doc would have prevented it?
   - What is reusable: a command, a script, a doc note?
3. Write to `docs/reflect/YYYY-MM-DD.md`. One file per day, append sections if the file exists.

## File format

```markdown
# Reflect YYYY-MM-DD

## <task name>
- Outcome: done | partial | escalated
- Slow path: what wasted time
- Root cause: the pattern behind the bug or friction
- Lesson: the check or doc that would have prevented it
- Reusable: command/script/doc worth keeping
```

## Constraints
- Be specific. "Should test earlier" is worthless; "run `bun test -t <name>` before dispatching fixer" is a lesson
- No blame, no padding. Skip the entry entirely if nothing was learned
- Never record secrets or credentials in reflect files
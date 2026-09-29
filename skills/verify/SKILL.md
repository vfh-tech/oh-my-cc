---
name: verify
description: "Evidence-before-done gate. Use before declaring any implementation, fix, or migration complete: enumerate acceptance criteria, run the real commands, show real output, and only then claim done. Use when the task involves \"verify\", \"check it works\", or closing out a plan."
---

# Verify

Done = evidence shown, not a summary written. Never claim success from a
successful-looking tool call alone; read back the changed state.

## When

- Before marking a task, plan step, or issue as complete
- After implementing or fixing anything the user asked for
- When the user says "verify", "check it", or "is it done?"

## Flow

1. **Criteria**: list acceptance criteria as checkboxes, from the request or spec.
   No criteria stated? Derive the minimal set that would catch a regression.
2. **Exercise**: run the real thing against real state:
   - Code change: run the relevant test/build command, capture output
   - Config/API/file write: read back the exact target, diff against intent
   - UI: confirm via the actual surface, not a description
3. **Evidence**: for each criterion, one line: criterion, command read back or
   run, pass/fail with the observed value.
4. **Verdict**: all criteria pass -> done, report evidence. Any fail -> report
   the failing criterion with output; do not round up to done.

## Rules

- Fabricated or assumed output is worse than an honest blocker. If a check
  cannot run, say so and say why.
- Verify the named target, not a proxy (deployed URL, merged PR, installed
  plugin path), when one exists.
- Keep evidence minimal: one command + one result per criterion, no essays.
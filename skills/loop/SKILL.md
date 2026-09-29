---
name: loop
description: "Auto-iterative execute-and-verify run. Use for /loop or when the user wants work repeated until a success criterion passes: fix failing tests until green, make build pass, iterate on lint errors. Escalates after max attempts."
---

# Loop Engineering

Execute work with an agent, verify against success criteria, repeat until done or escalated.

## 1. Plan
Parse from the invocation:
- **Goal**: the work (required)
- **Success criteria**: test | build | lint | fileExists | command | manual. Default: derived from the goal text. Multiple criteria allowed; all must pass
- **Max attempts**: default 5

Write the loop state to the session: goal, criteria, attempt count.

## 2. Execute
Dispatch one iteration to the matching agent:
- Code changes: fixer
- UI work: designer
- DB/API work: backend
- Test authoring/repair: tester
- Pure research/search tasks: explorer

Pass: goal, current attempt number, and the verbatim failure feedback from the previous iteration (none on attempt 1).

## 3. Verify
Run each success criterion via Bash yourself:
- test: run the test command; pass = exit 0
- build: run the build command; pass = exit 0
- lint: run the linter; pass = exit 0
- fileExists: check path exists
- command: run it; pass = exit 0
- oracle/manual: dispatch oracle or ask the user; pass = explicit confirmation
- Tester preference: for criteria `test`, prefer dispatching tester over running the suite yourself when a failing test needs interpretation or authoring new tests; the orchestrator still runs the final exit-code check

## 4. Decide
- All criteria pass → **done**. Report iterations used and final evidence.
- Fail and attempts < max → next iteration with the failure output as feedback. Never repeat an identical prompt; include what changed and what still fails.
- Fail at max attempts → **escalated**. Report: goal, criteria, per-iteration log, last failure, recommendation.

## Iteration log
One line per attempt: `attempt N: <change summary> -> <criterion> passed|failed (<evidence>)`.

## Rules
- Never loosen a criterion to force a pass
- Never mark done without running the criteria in step 3
- Scope each iteration to the smallest change that could flip the failing criterion
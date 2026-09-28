---
description: Iterate work with an agent until success criteria pass
allowed-tools: Task, Bash, Read, Grep, Glob
argument-hint: <goal> [success criteria]
---

Run the loop skill with the user's goal and criteria.

Goal and criteria: $ARGUMENTS

Follow `skills/loop/SKILL.md` exactly:

1. **Plan**: parse goal + success criteria (test/build/lint/fileExists/command/manual). If criteria omitted, derive them from the goal text and state them before the first iteration. Max attempts default 5.
2. **Execute**: dispatch one iteration per attempt to the matching agent (fixer for code, designer for UI, explorer for research). Include the previous iteration's verbatim failure feedback from attempt 2 onward.
3. **Verify**: run every criterion via Bash yourself. Never trust agent self-reports; pass requires the command's exit 0 or an explicit oracle/user confirmation.
4. **Decide**: all pass → done with evidence; else next iteration with new failure feedback, never an identical prompt; max reached → escalated with per-iteration log and recommendation.
5. Log one line per attempt: `attempt N: <change> -> <criterion> passed|failed (<evidence>)`.
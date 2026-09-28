---
description: Multi-model council consensus on a question
allowed-tools: Task, Read, Grep, Glob, WebFetch
argument-hint: <question>
---

Run the council skill with the user's question.

Question: $ARGUMENTS

Follow `skills/council/SKILL.md` exactly:

1. Read the active preset (see `commands/preset.md`); default lineup if none loaded: 3 seats, model `inherit` each, seat names alpha/beta/gamma.
2. Dispatch the `councillor` agent once per seat in ONE parallel message. Each dispatch gets the question verbatim plus any file paths/line refs the question references. No coordination between seats.
3. Collect all responses; record failed seats as failed, never omitted.
4. Synthesize the output in the required format: Council Response, Per-Councillor Details (by seat name), Council Summary with Consensus Level (unanimous | majority | split), Agreed Points, Disagreements + resolution, Remaining Uncertainty, Recommended Action.
5. Never dispatch councillors for trivial questions. If the question is trivial, answer directly and say the council was not warranted.
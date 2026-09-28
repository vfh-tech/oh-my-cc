---
name: council
description: Multi-model consensus decision. Use when the user asks for /council, a second opinion, or an important decision where several models should weigh in independently before one synthesized answer.
---

# Council

Multi-model consensus. Dispatch independent read-only councillors, synthesize.

## Preconditions
- Active preset loaded (see /preset). Without one, use model `inherit` for 2-3 councillors
- The question may include code context as file paths + line refs, never pasted file bodies

## Flow

1. **Lineup**: read the preset's councillor table: seat name + model per seat. 3-5 seats, all different models.
2. **Dispatch**: spawn the `councillor` agent once per seat, all in ONE parallel message. Pass each dispatch:
   - The original question, verbatim
   - Relevant file paths + line refs
   - Instruction: independent analysis, no coordination
3. **Collect**: gather all N responses. If one failed or timed out, note its status instead of omitting the seat.
4. **Synthesize** (the orchestrator does this directly, no extra agent):

```markdown
## Council Response
<best synthesized answer, integrating strongest points, resolving disagreements>

## Per-Councillor Details
<seat name> (<model>):
- Key insight: ...
- Confidence: ...
- Agreed/disagreed with: ...

## Council Summary
- **Consensus Level**: unanimous | majority | split
- **Agreed Points**: ...
- **Disagreements**: ... and the resolution
- **Remaining Uncertainty**: ...
- **Recommended Action**: ...
```

## Rules
- Never average responses. Choose the best approach and improve on it
- Credit seats by seat name, not model label
- Councillors never see each other's responses
- Do not dispatch councillors for trivial questions; answer directly instead

## ponytail
No compaction/checkpoint exception handling (v1 Claude Code). Add if sessions routinely compact mid-council.
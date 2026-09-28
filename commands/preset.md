---
description: Switch agent-model preset for the rest of the session
allowed-tools: Read, Glob
argument-hint: <preset-name>
---

Load the model override table for the rest of the session.

Preset name: $ARGUMENTS

Follow `commands/preset.md` + `presets/` conventions:

1. Read `presets/<name>.md`. If missing, list `presets/*.md` and ask which one.
2. Parse the preset's table: agent name → model + reason.
3. State the loaded mapping to the user as a table.
4. From now on, every Agent-tool dispatch in this session passes the preset's model for that agent as the `model` argument, overriding each agent file's frontmatter model. No preset loaded → use frontmatter models.
5. Platform note (state once when loading): Claude Code reads frontmatter `model` at session start; preset overrides apply at dispatch time via the Agent tool's model argument, not by changing agent files.
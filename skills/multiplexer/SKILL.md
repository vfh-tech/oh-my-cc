---
name: multiplexer
description: Live-view background subagents in tmux or zellij panes. Use when the user wants to watch subagent work in real time, when coordinating many parallel agents, or when /council or /loop runs with visible panes.
---

# Multiplexer

Mirror background subagents into tmux/zellij panes for live viewing.

## Detection
```bash
command -v tmux || command -v zellij
```
- tmux present: use tmux. Else zellij. Else: no multiplexer, use plain background tasks and tell the user. Do not install anything.

## Spawning a pane per subagent

tmux:
```bash
tmux new-window -t <session> -n <agent-name> 'claude -p "<prompt>" 2>&1 | tee /tmp/omocc-<agent-name>.log'
```

zellij:
```bash
zellij action new-pane --name <agent-name> -- bash -c 'claude -p "<prompt>" 2>&1 | tee /tmp/omocc-<agent-name>.log'
```

- One pane per subagent. Pane name = agent name.
- Prompt must be single-line-safe: replace newlines with `; ` and escape single quotes.
- Completion: poll `tmux list-panes -F "#{pane_dead}"` (or pane process exit) and read the log tail for the result. `tee` guarantees the output survives pane close.

## Rules
- Panes are observation only. Never send keys into an agent pane
- The dispatching of the actual subagent stays with the normal Agent tool; panes mirror the headless run for visibility
- On session end, leave panes open for the user to review; do not kill them unasked
- If a pane dies early, read its log tail and report the error

## Fallback
No tmux/zellij: state it, run the subagents as ordinary background tasks, deliver results as usual.

## ponytail
Approximation only. No true TUI pane embedding like OpenCode's client lifecycle. Replace if Claude Code ships native pane integration.
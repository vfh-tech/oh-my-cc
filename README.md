# oh-my-cc

Specialist agent orchestration plugin for Claude Code. Purely declarative: agents, skills, commands, and presets as markdown, zero runtime code. Ported from the concepts of [oh-my-opencode-slim](https://github.com/alvinunreal/oh-my-opencode-slim).

**Owner**: vfh-tech

## Install

```bash
claude plugin marketplace add /path/to/oh-my-cc
claude plugin install oh-my-cc@oh-my-cc
```

Requires Claude Code with subagent support.

## How it works

Your main Claude Code session acts as the **orchestrator**: it plans, delegates to specialists, reconciles results, and verifies. It is not the default implementation worker. Specialists do the work; the orchestrator manages it.

### Agents (`agents/*.md`)

| Agent | Role | Default model |
|-------|------|---------------|
| explorer | Fast codebase search, "where is X" | sonnet |
| librarian | Official docs, GitHub examples, library research | sonnet |
| oracle | Architecture, debugging strategy, code review (read-only) | opus |
| designer | UI/UX, styling, animation, visual polish | sonnet |
| fixer | Fast implementation from complete specs | sonnet |
| backend | API design, DB schema/migrations, query optimization | sonnet |
| tester | Test authoring, failing-test repair, coverage, edge cases | sonnet |
| safeguard | Security audit, threat modeling (read-only verdict) | opus |
| observer | Image/screenshot/PDF/diagram analysis (explicit dispatch only) | haiku |
| councillor | Read-only council advisor, one per model seat | inherit |

### Commands

- `/oh-my-cc:council <question>`: dispatch 3-5 independent read-only councillors on different models, synthesize with consensus rating (unanimous | majority | split)
- `/oh-my-cc:loop <goal> [criteria]`: iterate execute + verify until success criteria pass (test/build/lint/fileExists/command/manual), max 5 attempts, then escalate
- `/oh-my-cc:preset <name>`: load a model override table applied at dispatch time for the session

### Skills

- **deepwork**: orchestrator policy. Categorize, plan, dispatch parallel specialists, reconcile, verify
- **council**: multi-model consensus with per-seat details and consensus rating
- **loop**: auto-iterate execute + verify until criteria pass, escalate at max attempts
- **codemap**: generate `codemap.md` repository atlas
- **reflect**: post-task retrospective into `docs/reflect/YYYY-MM-DD.md`
- **worktrees**: isolated git worktree per feature
- **clonedeps**: read-only dependency clones under `.deps/repos/`
- **simplify**: behavior-preserving simplification pass
- **multiplexer**: live tmux/zellij panes mirroring background subagents

### Presets

- `balanced` (default): sonnet + opus
- `openai`: cross-provider mix, requires a gateway (LiteLLM or similar) exposing OpenAI models to Claude Code
- `budget`: haiku + sonnet floor, cost-optimized

Preset switching is dispatch-time override: Claude Code reads each agent's frontmatter `model` at session start, so `/preset` loads a table the orchestrator passes as the `model` argument on every Agent-tool dispatch. Frontmatter models apply when no preset is loaded.

## Multiplexer

If `tmux` or `zellij` is installed, the multiplexer skill mirrors background subagents into named panes running headless `claude -p` with log capture. Without one, plain background tasks. Approximation only, not native TUI embedding.

## Usage

### Automatic delegation (no command needed, just talk)

| You say | Dispatched to |
|---------|---------------|
| "find every caller of `calculateTotal`" | explorer |
| "what's the official Express error-handling pattern?" | librarian |
| "review this refactor for race conditions" | oracle |
| "make the product cards responsive" | designer |
| "add `phoneNumber` to the User model + migration" | backend |
| "add the endpoint DELETE /users/:id" | fixer or backend (API → backend) |
| "write tests for the div() edge cases" | tester |
| "audit auth flow before release" | safeguard |
| "read error.png, transcribe the error exactly" | observer |

Or explicit: `@agent-oh-my-cc:explorer find all routes` (typeahead lists all agents after `@agent-oh-my-cc:`).

### Explicit commands

```
/oh-my-cc:loop fix all failing tests until node --test exits 0
/oh-my-cc:council Zustand vs Jotai vs Redux Toolkit for our dashboard?
/oh-my-cc:preset budget
```

- **loop**: dispatches the matching agent per iteration, runs the criterion itself, max 5 attempts, then escalates
- **council**: 3 independent read-only councillors analyze in parallel; synthesized answer with consensus rating
- **preset**: loads a model table; every subsequent dispatch uses it

### Skills (auto-trigger from keywords)

"make a codemap" → codemap; "simplify this code" → simplify; "reflect on why this took long" → reflect; "isolate this work in a worktree" → worktrees; "clone the Zod source to inspect internals" → clonedeps.

### Fullstack pipeline example

```
"refactor the auth module: split session handling, update all callers, keep tests green"

orchestrator:
1. explorer    → map all session-handling call sites
2. fixer       → execute the refactor
3. backend     → adjust the session schema/migration if needed
4. tester      → write/repair tests for the new structure
5. safeguard   → audit the changed auth code
6. orchestrator→ run the suite, reconcile, report
```

### Checking what is installed

```bash
claude plugin details oh-my-cc | sed -n '6,14p'   # component inventory
claude plugin validate <plugin-dir>               # manifest + frontmatter check
ls ~/.claude/plugins/cache/oh-my-cc/oh-my-cc/*/agents/   # installed files
```

In a session: `/agents`, or type `@agent-oh-my-cc:` to see the typeahead.

## Layout

```
oh-my-cc/
├── .claude-plugin/
│   ├── plugin.json        # plugin manifest
│   └── marketplace.json   # local marketplace wrapper
├── agents/                # 10 subagent definitions
├── skills/                # 9 bundled skills
├── commands/              # /council, /loop, /preset
├── presets/               # balanced, openai, budget
└── README.md
```

## Verify

1. `claude` with the plugin loaded: `/agents` lists the 10 agents
2. Run each skill once end-to-end in a sample repo
3. `/oh-my-cc:council` with 3 parallel councillors produces a synthesis with rating
4. `/oh-my-cc:loop` on a small failing test finishes within 3 iterations
5. `/oh-my-cc:preset budget` changes the next dispatch's model
6. No file exceeds 200 lines

## Non-goals (v1)

No companion mascot, no cache-safety tripwire (Claude Code owns the prompt payload), no custom background job board (native subagents suffice), no runtime model switching (frontmatter model is static per session; presets cover it).
# oh-my-cc

Specialist agent orchestration plugin for Claude Code. Purely declarative: agents, skills, commands, and presets as markdown, zero runtime code. Ported from the concepts of [oh-my-opencode-slim](https://github.com/alvinunreal/oh-my-opencode-slim).

**Owner**: vfh-tech

## Install

```bash
claude plugin marketplace add vfh-tech/oh-my-cc
claude plugin install oh-my-cc@oh-my-cc            # user scope (default): all projects
claude plugin install oh-my-cc@oh-my-cc -s project # project scope: this repo only
```

Just the skills? Install from GitHub (no marketplace):

```bash
npx skills add vfh-tech/oh-my-cc
```

Or from the [skills.sh](https://skills.sh) pack:

```bash
npx skills add https://skills.sh/p/AJizdlxWBmPYUqSa
```

Requires Claude Code with subagent support.

## How it works

Your main Claude Code session acts as the **orchestrator**: it plans, delegates to specialists, reconciles results, and verifies. It is not the default implementation worker. Specialists do the work; the orchestrator manages it.

## Quick start

Three ways to use the plugin, from zero config to full control:

1. **Just talk.** Say what you want in plain language. The orchestrator picks the right specialist automatically. No commands, no setup.
2. **Slash commands** when you want a specific machine: `/oh-my-cc:council` for big decisions, `/oh-my-cc:loop` for iterate-until-green, `/oh-my-cc:preset` for cost control.
3. **Force a specialist** with `@agent-oh-my-cc:<name>` when you want exactly one agent, e.g. `@agent-oh-my-cc:reviewer review this diff`.

A typical first session:

```
> find every place we call the Stripe API          ← explorer, automatically
> add retries with backoff to those call sites     ← fixer, automatically
> write tests for the retry logic                  ← tester
> is this safe to ship? /oh-my-cc:council          ← 3 councillors vote
> /oh-my-cc:loop make all tests pass               ← iterate until green
```

You never chose an agent in that session. That is the point.

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
| reviewer | Code review of diffs/PRs: severity-rated findings, read-only | sonnet |
| integrator | PR lifecycle: branch hygiene, gh pr, CI watch, merge, cleanup | sonnet |
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
- **verify**: evidence-before-done gate; done = criteria checked with real output
- **release**: version bump + changelog + tag + push, preconditions first
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
| "review this PR before I merge it" | reviewer |
| "ship v1.3 with a changelog" | release skill |
| "read error.png, transcribe the error exactly" | observer |

Or explicit: `@agent-oh-my-cc:explorer find all routes` (typeahead lists all agents after `@agent-oh-my-cc:`).

### Explicit commands

```
/oh-my-cc:loop fix all failing tests until node --test exits 0
/oh-my-cc:council Zustand vs Jotai vs Redux Toolkit for our dashboard?
/oh-my-cc:preset budget
```

- **loop**: dispatches the matching agent per iteration, runs the criterion itself, max 5 attempts, then escalates. Use when a task has a hard pass/fail check: tests green, build exits 0, file exists.
- **council**: 3 independent read-only councillors analyze in parallel; synthesized answer with consensus rating (unanimous | majority | split). Use for architecture choices and "should we do X" decisions. Costs 3 model calls; do not use for trivia.
- **preset**: loads a model table; every subsequent dispatch uses it. `budget` for cheap lanes, `openai` to route some seats to OpenAI through a gateway.

### When to use what (cheat sheet)

| Situation | Reach for |
|-----------|-----------|
| "Where is X implemented?" | explorer (just ask, no command) |
| Recurring task with a pass/fail check | `/oh-my-cc:loop <task> <criterion>` |
| Two or more valid designs, you keep flip-flopping | `/oh-my-cc:council <question>` |
| Token bill too high | `/oh-my-cc:preset budget` |
| Fresh clone, missing deps | clonedeps skill |
| Work must not touch your current branch | worktrees skill |
| About to say "done" | verify skill: criteria + real output first |
| Ready to merge | integrator agent (PR, CI, merge) |
| Task done, want to learn from it | reflect skill |

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
5. reviewer    → review the diff: blockers first
6. safeguard   → audit the changed auth code
7. integrator  → branch, gh pr create, watch CI, merge
8. orchestrator→ verify acceptance criteria with real output, report
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
├── agents/                # 12 subagent definitions
├── skills/                # 11 bundled skills
├── commands/              # /council, /loop, /preset
├── presets/               # balanced, openai, budget
└── README.md
```

## Verify

1. `claude` with the plugin loaded: `/agents` lists the 12 agents
2. Run each skill once end-to-end in a sample repo
3. `/oh-my-cc:council` with 3 parallel councillors produces a synthesis with rating
4. `/oh-my-cc:loop` on a small failing test finishes within 3 iterations
5. `/oh-my-cc:preset budget` changes the next dispatch's model
6. Reviewer dispatch on a sample diff returns severity-rated findings
7. No file exceeds 200 lines

## Non-goals (v1)

No companion mascot, no cache-safety tripwire (Claude Code owns the prompt payload), no custom background job board (native subagents suffice), no runtime model switching (frontmatter model is static per session; presets cover it).
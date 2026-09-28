---
name: codemap
description: Generate or refresh codemap.md, a repository architecture atlas. Use when onboarding to a repo, before deep work in an unfamiliar folder, or when the user asks for an architecture map.
---

# Codemap

Generate `codemap.md` at the repo root: a compressed map of the repository.

## Steps

1. Read `AGENTS.md`, `CLAUDE.md`, and `README.md` first if present. Honor existing conventions.
2. If `codemap.md` already exists, read it. Refresh stale sections only; keep what is still accurate.
3. Build the map with these sections:
   - **Project Responsibility**: one paragraph, what this repo is
   - **System Entry Points**: table of path, role (manifests, main files, CLIs)
   - **Repository Directory Map**: table of directory, one-line responsibility. Nested subdirectories with their own complexity get their own `codemap.md` inside, referenced by link
   - **Runtime Control Flow**: numbered steps from startup to output
   - **Key Cross-Module Integration Points**: bullet list of the seams that matter
   - **Recommended Reading Order**: 3-5 numbered entries
4. Constraints:
   - Root codemap ≤200 lines
   - Every claim verifiable by reading the referenced file
   - No invented abstractions; name real files, real exports
   - Data flow over file lists: what feeds what

## Output

Write `codemap.md`. Report: sections written, line count, and what was stale and refreshed if updating an existing map.
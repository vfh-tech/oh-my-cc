---
name: release
description: Cut a release for this repo: version bump, changelog, tag, push. Use when the user says "release", "cut a release", "ship vX.Y", or asks to publish a new version. Asks for nothing; reads state, proposes the plan, executes it.
---

# Release

Deterministic release path. Read state first, execute after.

## Preconditions

- Working tree clean (`git status`); otherwise stop and say what's dirty
- Version source of truth detected first: package.json / pyproject.toml /
  Cargo.toml / plugin.json / manifest. If multiple, bump them all or stop and
  ask which one

## Flow

1. **Read state**: current version, last tag, `git log <last-tag>..HEAD --oneline`
2. **Propose**: one line, version from -> to (semver: fix=patch, feature=minor,
   breaking=major), the changelog highlights from the log. Wait for "go" unless
   the user already named the version
3. **Execute**: bump every version field, update changelog (date + highlights),
   commit `release vX.Y.Z`, tag `vX.Y.Z`, push branch + tags
4. **Evidence**: tag visible on remote, bumped file read back, changelog entry
   present. Report links, not intentions

## Rules

- Never force-push tags; a wrong tag is deleted and re-cut on the remote, never
  rewritten under the same name locally then pushed
- Changelog highlights come from the actual log, not from memory of the sprint
- If CI/publish workflow exists, trigger or watch it; a release without CI
  green is not shipped
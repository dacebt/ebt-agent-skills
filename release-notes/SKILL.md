---
name: release-notes
description: >
  Generate release notes from git history. Produces two documents: a technical
  changelog with commit references, and a user-facing changelog in plain language.
  Use when preparing a release, reviewing what changed since the last tag, or when
  asked for a changelog or release summary. Do NOT use for commit message cleanup,
  git log formatting, or documentation unrelated to releases.
---

# Release Notes Generator

Review git history and code changes, then produce two release documents:
a technical changelog and a user-facing changelog.

## Scope

Determine the commit range to review:

1. If the user specifies a range, use it exactly
2. Otherwise, find the most recent tag: `git describe --tags --abbrev=0 2>/dev/null`
3. If a tag exists, use `<tag>..HEAD`
4. If no tags exist, use the last 50 commits: `git log -50 --oneline`
5. If the range exceeds 200 commits, stop and ask the user to narrow it

Report the range you're using before proceeding.

## Gather changes

```bash
# List commits in range
git log <range> --pretty=format:"%h %s" --no-merges

# For each commit, review the actual diff
git show <hash> --stat
git show <hash>
```

Read every commit message AND every diff. Commit messages alone are unreliable —
the code tells you what actually changed.

## Classify each change

Assign every change exactly one category and one impact level.

**Categories:**
- **feature** — new capability, endpoint, command, UI element, or config option
- **fix** — corrects broken behavior
- **breaking** — removes, renames, or changes behavior of something that exists (API signatures, config keys, CLI flags, database schemas, environment variables)
- **security** — patches a vulnerability or hardens access control
- **dependency** — adds, removes, or updates a dependency
- **refactor** — internal restructure with no behavior change
- **docs** — documentation only

**Impact levels (check in order, first match wins):**
- **Breaking** — any public API signature changed, config format changed, required environment variable added/removed, database migration required, CLI flag removed/renamed
- **User-visible** — changes something a user sees, interacts with, or depends on (UI, output format, performance, error messages, new features, bug fixes)
- **Internal** — refactors, test changes, CI changes, dependency bumps with no behavior change, documentation

**Grouping:** Multiple commits that address the same logical change should be merged
into a single entry. Reference all related commit hashes but write one description.

## Write the documents

Produce both documents in the working directory.

---

### Document 1: `changelog.technical.md`

Audience: developers, code reviewers, ops teams.

Structure:
```markdown
# Changelog — [version or range]

**Range:** `<start>..<end>`
**Date:** YYYY-MM-DD

## Breaking Changes

- Description of what changed and what breaks. Include migration steps. (`abc1234`, `def5678`)

## Security

- What was patched and severity if known. (`abc1234`)

## Features

- What was added, where it lives, how to use it. (`abc1234`)

## Bug Fixes

- What was broken, what triggered it, what the fix does. (`abc1234`)

## Dependencies

- Dependency name: old version → new version. Note why if relevant. (`abc1234`)

## Internal

- Refactors, test improvements, CI changes. Brief. (`abc1234`)
```

Rules for this document:
- Every commit in the range must appear in at least one section
- Include commit short hashes as parenthetical references
- Breaking changes must include migration guidance (what the reader needs to do)
- Omit empty sections entirely
- Order sections as shown — breaking changes and security always come first

---

### Document 2: `changelog.user.md`

Audience: end users, product stakeholders, non-developers.

Structure:
```markdown
# What's New — [version or range]

**Date:** YYYY-MM-DD

## New Features

- One sentence per feature. Describe the outcome, not the implementation.
  Example: "You can now export reports as PDF from the dashboard."

## Fixes

- One sentence per fix. Describe what was wrong, not how it was fixed.
  Example: "Fixed an issue where search results wouldn't load when using filters."

## Important Changes

- Only include breaking changes and security fixes that affect users.
- State what changed and what the user needs to do (if anything).
  Example: "The config file format has changed. Run `app migrate-config` to update yours."
```

Rules for this document:
- Only include changes with impact level **Breaking** or **User-visible**
- No commit hashes, no file paths, no function names, no technical jargon
- Write in second person ("you can now...") or neutral voice ("search results now...")
- Each bullet should be understandable by someone who has never read the code
- If no user-visible changes exist, state that explicitly
- Omit empty sections entirely

---

## Verify completeness

After writing both documents:

1. Count commits in range: `git rev-list --count <range> --no-merges`
2. Count entries in `changelog.technical.md` (accounting for grouped commits)
3. Confirm every commit hash from the range appears in the technical changelog
4. Confirm no internal-only changes leaked into `changelog.user.md`

If any commits are unaccounted for, go back and classify them.

## Quality bar

- [ ] Commit range is stated at the top of both documents
- [ ] Every commit in range appears in the technical changelog
- [ ] Breaking changes have migration guidance in both documents
- [ ] User changelog contains zero jargon, file paths, or commit references
- [ ] Empty sections are omitted
- [ ] Both files are written to the working directory

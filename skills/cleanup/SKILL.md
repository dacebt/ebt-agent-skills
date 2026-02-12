---
name: code-cleanup-review
description: >
  Review changed or staged code for structural cleanup: redundancy, dead code, rule
  violations, and low-value noise. Use when reviewing diffs, PRs, or modified files
  before commit. Do NOT use for feature work, architectural redesign, style rewrites
  unrelated to the diff, or speculative refactors. Reports findings only — does not
  apply changes unless explicitly instructed.
---

# Code Cleanup Review

Perform a focused structural review on changed code. Identify redundancy, rule violations,
and noise while preserving intent and runtime behavior. This is a read-only review by
default — report findings, do not apply fixes unless told to.

## Scope

Determine what to review, in this order:

1. **Explicit paths provided** — review only those files
2. **Staged files** — `git diff --cached --name-only`
3. **Modified files** — `git diff --name-only`
4. Stop at the first non-empty set. Never expand beyond changed files unless directed.

If no changed files are found, report that and stop.

## Workflow

### 1. Gather context

For each file in scope:

- Read the full file, not just the diff — you need surrounding context to judge intent
- Identify the relevant project conventions (lint config, theme system, design tokens, etc.)

### 2. Run automated checks (if available)

```bash
# Attempt lint — adapt command to what the project uses
npm run lint --silent 2>/dev/null || npx eslint . 2>/dev/null || true

# Attempt type check
npx tsc --noEmit 2>/dev/null || true
```

Note failures or warnings that apply to files in scope. Ignore noise from unchanged files.

### 3. Structural review

Check each file for these categories:

**Redundancy**

- Variables assigned once and used once (inline the value)
- Wrapper functions that pass through without adding logic
- Redundant props (passed but unused, or duplicating defaults)
- Unused imports or exports

**Dead code**

- Unreachable branches (after returns, impossible conditions)
- Commented-out code blocks (not section headers or explanatory comments)
- Unused functions, classes, or type definitions within the diff scope

**Noise**

- Comments that restate what the code obviously does
- Console.log / debug statements left behind
- Excessive whitespace changes mixed with logic changes

**Convention violations**

- Hardcoded values that should use project tokens (colors, spacing, breakpoints)
- Patterns that contradict project lint rules or documented conventions
- Inconsistency with adjacent unchanged code in the same file

**Preserve these — do NOT flag:**

- Section/region comments that organize code structure
- Comments explaining _why_ (not _what_), edge cases, or workarounds
- TODOs with actionable context

### 4. Report

For each finding, report:

- **File** — path relative to repo root
- **Line(s)** — line number or range
- **Category** — one of: redundancy, dead-code, noise, convention
- **Issue** — what's wrong, specifically
- **Fix** — what to do about it, concisely

If no issues found, report: "No issues found in [N] files reviewed."

Do not propose behavioral changes, logic rewrites, or architectural suggestions.

## Applying fixes

Only when the user explicitly asks you to apply fixes:

1. Apply changes file by file
2. After each file, re-run available lint/type checks
3. Verify no runtime behavior changed (same test results, same types)
4. Summarize what was changed per file

## Quality bar

The review is complete when:

- [ ] All files in scope have been examined with surrounding context
- [ ] Automated checks ran (or were confirmed unavailable)
- [ ] Every finding has a specific file, line, category, and fix
- [ ] No behavioral or architectural changes are proposed
- [ ] Preserved comments and intentional patterns are left intact

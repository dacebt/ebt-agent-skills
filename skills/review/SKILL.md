---

name: cleanup-review
description: >
  Review changed or staged code for cleanup-grade issues within the diff boundary.
  Use before commit/PR or when reviewing a diff. Reports findings only.

---------------------------------------------------------------------

[BOUNDARY]
No edits. No patches. No file changes.
Stay inside the diff boundary.

Diff boundary definition:

* Findings must be tied to changed hunks OR within ±25 lines of a changed hunk in a file in scope.

* Surrounding context may be read for intent, but do not flag unrelated cleanup elsewhere.

* Review only files in scope.

* Do not expand to “similar” files.

* Do not propose behavioral changes or architectural redesign.

* Fixes are described as text only.

[SCOPE]
Resolve review scope in this order. Stop at the first non-empty set.

1. Explicit paths provided by the user
2. Staged files: `git diff --cached --name-only`
3. Modified files: `git diff --name-only`

If no changed files are found, report that and stop.

Resolved scope evidence:

* Print the exact file list selected by [SCOPE] (repo-relative).
* Print which scope source was used (explicit paths | staged | modified).
* Print the exact diff command used to establish the boundary.

[WORKFLOW]
Per file:

* Read the full file, not just the diff.
* Use surrounding context to judge intent.
* Note local conventions from adjacent code and repo rules.

Automated checks (best-effort):

* Prefer host built-in tools over shell commands when available (Codex/Claude Code):

  * Changed-files / diff tool to resolve scope and boundary
  * File viewer tool to read full in-scope files
  * Diagnostics tool (lint/typecheck/test) if the host provides it
  * Repo search tool only when it can be restricted to in-scope paths
* If a built-in tool is unavailable, fall back to equivalent local commands.
* Report only output that clearly maps to files in scope.

[REVIEW]
Flag only cleanup-grade issues inside the diff boundary.

Redundancy:

* Values introduced that are assigned once and used once where inlining is clearer.
* Pass-through wrappers added without semantics.
* Props/args added but unused or duplicating defaults.
* Imports/exports added but unused in the final state.

Dead code:

* Unreachable branches introduced by the change (after return/throw).
* Impossible conditions introduced by the change.
* Commented-out code blocks (not documentation/sectioning).
* New symbols introduced but never referenced (functions/types/constants).

Noise:

* Debug logs left in.
* Whitespace-only hunks mixed with logic hunks.
* “What” comments that restate obvious code.

Convention:

* Hardcoded tokens where the file clearly uses tokens (colors/spacing/typography/breakpoints).
* Patterns that conflict with adjacent conventions in the same file.
* Inconsistency with adjacent unchanged code in the same file.

Do not flag:

* Section/region comments.
* “Why” comments (rationale, edge cases, workarounds).
* TODOs with actionable context.

[REPORT]
For each finding include:

* File (repo-relative path)
* Lines (number or range)
* Category (redundancy | dead-code | noise | convention)
* Issue (specific)
* Fix (minimal, concrete; text only)

Optional fields (when useful):

* Evidence (diff excerpt or command output snippet) that anchors the finding to the boundary

If no issues: "No issues found in <N> files reviewed."

[QUALITY_BAR]
Complete when:

* All files in scope read with surrounding context
* Resolved scope evidence included
* Every finding has file + lines + category + fix
* No behavioral/architectural suggestions
* No edits were applied

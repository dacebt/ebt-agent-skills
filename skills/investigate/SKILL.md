---
name: investigate
description: Generates a thorough, read-only investigative report when analyzing bugs, unclear behavior, architectural confusion, or unexpected system states. Use when diagnosing issues or understanding complex code paths.
---

[YOUR ASSIGNMENT]
Perform a deep, read-only investigation of a codebase issue and generate a structured Markdown report saved at the repository root.
Output is a comprehensive investigation report; not a fix, not an implementation plan.
You do not modify code, you do not run builds, you do not change system state.

If missing information prevents accurate investigation, ask 1–2 targeted clarifying questions before proceeding.

[OPERATIONAL CONSTRAINTS]
- Read-only investigation only.
- No file edits.
- No file writes except final Markdown report.
- No commits or git operations.
- No running dev servers.
- No builds.
- No system state changes.
- No non-readonly tools.
- No assumptions based on a single file; surrounding context must be reviewed.
- No emojis in the report.
- No timelines in the report.

[INVESTIGATION PROCESS]
1) Clarify Scope (If Needed)
If the request is ambiguous:
- Narrow scope with 1–2 precise questions.
- Confirm exact failure surface or expected behavior.
Do not proceed until scope is sufficiently defined.

1) Context Expansion
When reviewing a file:
- Identify imports and dependencies.
- Identify callers and consumers.
- Identify related domain modules.
- Identify boundary layers (API, LLM, storage, UI).
- Review adjacent files in the same module.
- Trace execution path across packages if applicable.
Never assume correctness from local inspection only.

1) Trace Execution Path
Document:
- Entry point(s)
- State transitions
- Data transformations
- Schema validation points
- Error handling paths
- Side effects
- Retry or idempotency mechanisms (if present)

If deterministic systems exist (e.g., seeded RNG), verify determinism boundaries.
If schema validation exists (Zod/Pydantic), confirm validation behavior and fallback handling.

4) Identify Failure Vectors
Explicitly evaluate:
- Type mismatches or unsafe casts
- Silent fallbacks
- Missing boundary validation
- Domain logic duplication
- Versioning incompatibilities
- Migration inconsistencies
- Race conditions or retry loops
- Cross-layer leakage (UI containing domain logic)

Do not speculate. Only document findings supported by code evidence.

5) Produce Root-Level Report
Save a Markdown file at the repository root using this filename pattern:
INVESTIGATION_REPORT_<slug>.md
Where <slug> is derived from the issue topic.

[REPORT OUTPUT]
Write a Markdown report to repository root:
INVESTIGATION_REPORT_<slug>.md

Slug rules:
- Derived from issue topic.
- Use a stable, filesystem-safe slug.

[REPORT STRUCTURE]
# Investigation Report: <Title>

## 1. Summary
Concise description of the issue and investigation scope.

## 2. System Context
High-level architecture relevant to the issue.

## 3. Execution Trace
Step-by-step logical flow of the system behavior.

## 4. Evidence
Concrete references:
- File paths
- Functions
- Code snippets (minimal, focused)
- Data flow points

## 5. Findings
Clear statements of:
- Confirmed behavior
- Misalignment with expectations
- Architectural inconsistencies
- Risk surfaces
No speculation beyond code-supported conclusions.

## 6. Root Cause (If Identifiable)
Explicit causal explanation. If not identifiable, state why.

## 7. Contributing Factors
Structural or architectural patterns contributing to the issue.

## 8. Risk Assessment
Impact surface:
- Data integrity
- Determinism
- Validation boundaries
- User-facing behavior
- Retry/idempotency risks

## 9. Recommended Next Investigation Steps
Only investigative next steps.
No implementation plans.
No timelines.

[QUALITY STANDARD]
The report must:
- Be thorough.
- Be technically precise.
- Avoid filler language.
- Avoid emotional tone.
- Avoid emojis.
- Avoid timelines.
- Avoid implementation planning.
- Reference multiple files where applicable.
- Demonstrate contextual understanding across module boundaries.

[DEFINITION OF DONE]
Investigation is complete when:
- Surrounding modules have been reviewed.
- Execution flow is fully documented.
- Boundary validations are verified.
- Determinism and retry paths (if applicable) are evaluated.
- Findings are supported by direct code references.
- Report is saved at repository root.

No changes to code are performed.

---
name: contextual-investigator
description: Generates a thorough, read-only investigative report when analyzing bugs, unclear behavior, architectural confusion, or unexpected system states. Use when diagnosing issues or understanding complex code paths. Do NOT use for implementing fixes or making code changes.
---

# Contextual Investigator

## Purpose

Perform a deep, read-only investigation of a codebase issue and generate a structured Markdown report saved at the repository root. This skill does not modify files, run builds, or change system state. It analyzes surrounding context, traces logic paths, and documents findings clearly.

The output is a comprehensive investigation report — not a fix and not an implementation plan.

---

## When to Use

Use when:

- A bug or regression needs root cause analysis.
- System behavior is unclear or inconsistent.
- A feature’s architecture must be understood before refactoring.
- Logs or runtime behavior contradict expectations.
- A prior agent change produced unintended side effects.
- There is ambiguity in domain logic placement.

Do NOT use when:

- The task is to implement a feature.
- The task is to refactor or modify code.
- The task requires running dev servers or builds.
- The goal is to propose timelines or execution sequencing.

---

## Operational Constraints (Strict)

- Read-only investigation only.
- No file edits.
- No file writes except final Markdown report.
- No commits or git operations.
- No running dev servers.
- No builds.
- No system state changes.
- No non-readonly tools.
- No assumptions based on a single file — surrounding context must be reviewed.
- No emojis in the report.
- No timelines in the report.

If missing information prevents accurate investigation, ask 1–2 targeted clarifying questions before proceeding.

---

## Investigation Process

### 1. Clarify Scope (If Needed)

If the request is ambiguous:
- Narrow scope with 1–2 precise questions.
- Confirm exact failure surface or expected behavior.

Do not proceed until scope is sufficiently defined.

---

### 2. Context Expansion

When reviewing a file:

- Identify imports and dependencies.
- Identify callers and consumers.
- Identify related domain modules.
- Identify boundary layers (API, LLM, storage, UI).
- Review adjacent files in the same module.
- Trace execution path across packages if applicable.

Never assume correctness from local inspection only.

---

### 3. Trace Execution Path

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

---

### 4. Identify Failure Vectors

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

---

### 5. Produce Root-Level Report

Save a Markdown file at the repository root using this filename pattern:

INVESTIGATION_REPORT_<slug>.md

Where `<slug>` is derived from the issue topic.

---

## Report Structure (Mandatory)

The report must contain the following sections:

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

---

## Quality Standard

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

---

## Definition of Done

Investigation is complete when:

- Surrounding modules have been reviewed.
- Execution flow is fully documented.
- Boundary validations are verified.
- Determinism and retry paths (if applicable) are evaluated.
- Findings are supported by direct code references.
- Report is saved at repository root.

No changes to code are performed.

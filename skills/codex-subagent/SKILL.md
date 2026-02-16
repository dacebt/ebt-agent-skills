---
name: codex-subagent
description: >
  Delegate subtasks to Codex CLI via subprocess execution for isolated execution,
  parallel work, second-opinion analysis, or mechanically intensive coding tasks.
  Use when a self-contained prompt is possible and Codex-specific delegation is
  desired. Do NOT use for trivial tasks, sensitive secrets in CLI args, or work
  requiring continuous user dialogue.
---

# Codex Shadow Clone Protocol

Invoke Codex CLI as a subprocess to delegate self-contained subtasks.
The subagent has no conversation context, so prompts must be complete.

## When to delegate

Delegate when any of these apply:

- The subtask is **self-contained** and can be described without your full conversation context
- You want **isolated execution** (changes in a separate worktree, no risk to current state)
- The work would benefit from a **parallel track** (you continue while it runs)
- You want a **second opinion** on architecture, implementation, or correctness
- The subtask is **mechanically intensive** (large refactors, bulk file operations, test runs)

Do NOT delegate when:

- The task is faster to do yourself than to describe to another agent
- The task requires ongoing back-and-forth dialogue with the user
- You cannot formulate a self-contained prompt (too much ambient context required)
- The task involves secrets, credentials, or sensitive data that should not appear in CLI args

## Provider command

Before invoking, verify binary availability: `command -v codex &>/dev/null`.

| Agent             | Binary  | Invocation                           |
| ----------------- | ------- | ------------------------------------ |
| Codex CLI         | `codex` | `codex exec "<task>"`               |
| Codex (full-auto) | `codex` | `codex exec --full-auto "<task>"`   |

Use `codex exec` by default. Use `--full-auto` only when explicitly requested.

## Writing the task prompt

A good task prompt includes:

1. **What to do** — specific, concrete outcome
2. **Where to work** — repo path, branch, relevant files
3. **Constraints** — what NOT to change, style requirements, dependencies to respect
4. **How to verify** — command to run that proves success
5. **What to return** — summary, diff, file path, or yes/no answer

## Execution

### Standard invocation

```bash
codex exec "<task>"
```

### Isolated execution with git worktrees

```bash
WORKTREE_PATH=".worktrees/agent-task"

git worktree add "${WORKTREE_PATH}" HEAD --detach 2>/dev/null
cd "${WORKTREE_PATH}" && codex exec "<task>"
```

### Review and integrate changes from a worktree

```bash
git -C "${WORKTREE_PATH}" diff
git -C "${WORKTREE_PATH}" log --oneline HEAD~3..HEAD
git cherry-pick <commit-hash>

git -C "${WORKTREE_PATH}" diff > /tmp/agent-changes.patch
git apply /tmp/agent-changes.patch

git worktree remove "${WORKTREE_PATH}" --force 2>/dev/null
git worktree prune
```

Always review agent changes before integrating.

## Handling failure

1. Simplify the task and retry once
2. Do the work yourself
3. Do NOT retry the same prompt more than once

## Quality bar

A delegation is complete when:

- [ ] The subagent has finished and you've reviewed what it did
- [ ] File changes (if any) have been inspected before integration
- [ ] The result has been verified (tests pass, build succeeds, review criteria met)
- [ ] Worktrees have been cleaned up
- [ ] The user has been informed of what was delegated and what came back

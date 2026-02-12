---
name: collaboration-mode
description: >
  Delegate subtasks to other coding agents via CLI. Use when a task benefits from
  isolated execution, parallel work, a second opinion, or specialized analysis.
  Do NOT use for trivial tasks you can complete faster yourself, or when the task
  requires your active conversation context that cannot be summarized in a prompt.
---

# Agent Collaboration Protocol

You can invoke other coding agents as CLI subprocesses to delegate work. Each agent
runs in isolation with no access to your conversation history — you must give it
everything it needs in the task prompt.

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

## Agent registry

Before invoking an agent, verify it exists: `command -v <binary> &>/dev/null`.

| Agent             | Binary         | Invocation                        |
| ----------------- | -------------- | --------------------------------- |
| Claude Code       | `claude`       | `claude -p "<task>"`              |
| Codex CLI         | `codex`        | `codex exec "<task>"`             |
| Codex (full-auto) | `codex`        | `codex exec --full-auto "<task>"` |
| Cursor Agent      | `cursor-agent` | `cursor-agent chat "<task>"`      |

If the binary is not found, skip that agent. Use a different available agent or do
the work yourself.

## Writing the task prompt

This is the most important step. The subagent has **zero context** about your conversation,
your repository state, or what you've already tried. The task prompt must be self-contained.

A good task prompt includes:

1. **What to do** — specific, concrete outcome (not "fix the bug" but "fix the TypeError in `src/auth/login.ts:47` where `user.email` is accessed before null check")
2. **Where to work** — repo path, branch, relevant files
3. **Constraints** — what NOT to change, style requirements, dependencies to respect
4. **How to verify** — command to run that proves success (test command, type check, build)
5. **What to return** — what you need back (a summary, a diff, a file path, a yes/no answer)

Good example:

```
Review src/api/handlers/ for error handling gaps. For each handler, check that:
(1) all async calls have try/catch, (2) error responses use the ApiError class from
src/errors.ts, (3) no raw exception details leak to the client. Report findings as
a list of file:line pairs with the issue category. Do not make changes.
```

Bad example:

```
Check the error handling in our API.
```

## Execution

### Standard invocation

Invoke the agent and let it work. It operates directly on the codebase — modifying files,
running commands, making commits — just like you do. When it finishes, review what changed.

```bash
claude -p "<task>"
```

### Isolated execution with git worktrees

Use worktrees when the subagent will **modify files** and you want to review before
those changes touch your working tree. Skip for read-only tasks (analysis, review,
information gathering).

```bash
WORKTREE_PATH=".worktrees/agent-task"

# Create worktree from current HEAD
git worktree add "${WORKTREE_PATH}" HEAD --detach 2>/dev/null

# Invoke agent scoped to the worktree
cd "${WORKTREE_PATH}" && claude -p "<task>"
```

### Review and integrate changes from a worktree

```bash
# See what the agent changed
git -C "${WORKTREE_PATH}" diff

# Option A: cherry-pick if the agent committed
git -C "${WORKTREE_PATH}" log --oneline HEAD~3..HEAD
git cherry-pick <commit-hash>

# Option B: apply uncommitted changes as a patch
git -C "${WORKTREE_PATH}" diff > /tmp/agent-changes.patch
git apply /tmp/agent-changes.patch

# Clean up
git worktree remove "${WORKTREE_PATH}" --force 2>/dev/null
git worktree prune
```

Always review agent changes before integrating. You are responsible for the final result.

## Handling failure

If the subagent produces bad results or fails:

1. Try a different available agent with the same task prompt
2. Simplify the task — break it into smaller pieces
3. Do the work yourself
4. Do NOT retry the same prompt with the same agent more than once

## Quality bar

A delegation is complete when:

- [ ] The subagent has finished and you've reviewed what it did
- [ ] File changes (if any) have been inspected before integration
- [ ] The result has been verified (tests pass, build succeeds, review criteria met)
- [ ] Worktrees have been cleaned up
- [ ] The user has been informed of what was delegated and what came back

# EBT Agent Skills

A collection of portable agent skills that work across Claude Code, Cursor, Codex CLI, and Gemini CLI.

Skills are self-contained directories with a `SKILL.md` file that teaches coding agents how to perform specific tasks. They follow the [Agent Skills open standard](https://agentskills.io), so you write them once and use them everywhere.

## Skills

| Skill | Description |
|---|---|
| **cleanup** | Structural code review for redundancy, dead code, and convention violations. Read-only by default. |
| **collab** | Delegate subtasks to other coding agents (Claude Code, Codex, Cursor) via CLI subprocesses. |

## Installation

All platforms use the same `SKILL.md` format. The only difference is where the files go.

### Claude Code

Claude Code discovers skills from `~/.claude/skills/` (global) and `.claude/skills/` (project-level). Project skills take precedence.

**Global install (available in all projects):**
```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/skills.git /tmp/agent-skills

# Copy individual skills
cp -r /tmp/agent-skills/skills/cleanup ~/.claude/skills/cleanup
cp -r /tmp/agent-skills/skills/collab ~/.claude/skills/collab
```

**Project install (scoped to one repo):**
```bash
cd your-project
mkdir -p .claude/skills

cp -r /path/to/skills/cleanup .claude/skills/cleanup
cp -r /path/to/skills/collab .claude/skills/collab
```

Skills are available immediately. Claude auto-discovers them and can invoke via `/cleanup` or `/collab`, or load them automatically when relevant.

Docs: [Claude Code Skills](https://code.claude.com/docs/en/skills)

---

### Cursor

Cursor discovers skills from `~/.cursor/skills/` (global) and `.cursor/skills/` (project-level). Announced in the January 2026 release.

**Global install:**
```bash
cp -r /path/to/skills/cleanup ~/.cursor/skills/cleanup
cp -r /path/to/skills/collab ~/.cursor/skills/collab
```

**Project install:**
```bash
cd your-project
mkdir -p .cursor/skills

cp -r /path/to/skills/cleanup .cursor/skills/cleanup
cp -r /path/to/skills/collab .cursor/skills/collab
```

Skills can be invoked via the slash command menu or triggered automatically when the agent matches your request to a skill description.

> **Note:** `~/.cursor/skills-cursor/` is reserved for Cursor's built-in skills. Don't install custom skills there.

---

### Codex CLI

Codex discovers skills from `~/.codex/skills/` (global) and `.agents/skills/` (project-level, scanned from working directory up to repo root).

**Global install:**
```bash
cp -r /path/to/skills/cleanup ~/.codex/skills/cleanup
cp -r /path/to/skills/collab ~/.codex/skills/collab
```

**Project install:**
```bash
cd your-project
mkdir -p .agents/skills

cp -r /path/to/skills/cleanup .agents/skills/cleanup
cp -r /path/to/skills/collab .agents/skills/collab
```

Invoke explicitly with `/skills` or `$skill-name` in your prompt, or let Codex match implicitly by description.

You can also install via the built-in installer inside a Codex session:
```
$skill-installer install cleanup
```

To disable a skill without deleting it, add to `~/.codex/config.toml`:
```toml
[[skills.config]]
path = "/path/to/skill/SKILL.md"
enabled = false
```

Restart Codex after adding new skills if they don't appear automatically.

Docs: [Codex Agent Skills](https://developers.openai.com/codex/skills/)

---

### Gemini CLI

Gemini CLI discovers skills from `~/.gemini/skills/` (user-scoped) and `.gemini/skills/` (workspace-scoped). Workspace skills override user skills when names collide. Currently in preview — install via `npm install -g @google/gemini-cli@preview`.

**User install:**
```bash
mkdir -p ~/.gemini/skills
cp -r /path/to/skills/cleanup ~/.gemini/skills/cleanup
cp -r /path/to/skills/collab ~/.gemini/skills/collab
```

**Workspace install:**
```bash
cd your-project
mkdir -p .gemini/skills

cp -r /path/to/skills/cleanup .gemini/skills/cleanup
cp -r /path/to/skills/collab .gemini/skills/collab
```

Gemini auto-discovers skills at session start. When it matches your request to a skill, it calls `activate_skill` and asks for your confirmation before loading.

Manage skills within a session:
```
/skills list
/skills enable cleanup
/skills disable collab
```

Docs: [Gemini CLI Agent Skills](https://geminicli.com/docs/cli/skills/)

---

### Symlink for shared installs

If you use multiple platforms and want a single source of truth, symlink from one platform's directory to the others:

```bash
# Use Claude Code as the canonical location
cp -r /path/to/skills/cleanup ~/.claude/skills/cleanup
cp -r /path/to/skills/collab ~/.claude/skills/collab

# Symlink for other platforms
ln -s ~/.claude/skills/cleanup ~/.cursor/skills/cleanup
ln -s ~/.claude/skills/cleanup ~/.codex/skills/cleanup
ln -s ~/.claude/skills/cleanup ~/.gemini/skills/cleanup

# Repeat for collab
ln -s ~/.claude/skills/collab ~/.cursor/skills/collab
ln -s ~/.claude/skills/collab ~/.codex/skills/collab
ln -s ~/.claude/skills/collab ~/.gemini/skills/collab
```

Or symlink the entire skills directory if all your skills should be shared:
```bash
ln -s ~/.claude/skills ~/.codex/skills
ln -s ~/.claude/skills ~/.gemini/skills
```

Codex and Gemini CLI both follow symlinks during skill discovery.

## Skill format

Each skill is a directory containing a `SKILL.md` with YAML frontmatter:

```yaml
---
name: skill-name
description: >
  What the skill does. When to use it. When NOT to use it.
---

# Instructions the agent follows when the skill is activated.
```

The `name` becomes the slash command (e.g., `/cleanup`). The `description` is what agents use to decide when to load the skill automatically — keep it precise.

Optional supporting directories:
```
skill-name/
├── SKILL.md           # Required
├── scripts/           # Executable scripts the agent can run
├── references/        # Documentation loaded on demand
└── assets/            # Templates, schemas, config files
```

## Contributing

1. Create `skills/<skill-name>/SKILL.md` with frontmatter and instructions
2. Register in `marketplace.json`
3. Test across at least one platform before submitting

## Quick reference

| Platform | Global path | Project path | Docs |
|---|---|---|---|
| Claude Code | `~/.claude/skills/` | `.claude/skills/` | [code.claude.com](https://code.claude.com/docs/en/skills) |
| Cursor | `~/.cursor/skills/` | `.cursor/skills/` | [cursor.com](https://docs.cursor.com) |
| Codex CLI | `~/.codex/skills/` | `.agents/skills/` | [developers.openai.com](https://developers.openai.com/codex/skills/) |
| Gemini CLI | `~/.gemini/skills/` | `.gemini/skills/` | [geminicli.com](https://geminicli.com/docs/cli/skills/) |

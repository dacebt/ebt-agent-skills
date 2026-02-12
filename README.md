# EBT Agent Skills

[![Read Before Installing](https://img.shields.io/badge/READ%20BEFORE-INSTALLING-critical?style=for-the-badge)](#safety-notice)
> **Safety Notice:** Review each skill's `SKILL.md` before installing. Skills are used at your own risk because they can direct an agent to run commands, edit files, and interact with external tools. A quick review helps you confirm the behavior matches your intent and security expectations.

A collection of portable agent skills that work across Claude Code, Cursor, Codex CLI, and Gemini CLI.

Skills are self-contained directories with a `SKILL.md` file that teaches coding agents how to perform specific tasks. They follow the [Agent Skills open standard](https://agentskills.io), so you write them once and use them everywhere.

## Safety Notice

Review each skill's `SKILL.md` before installing. Skills are used at your own risk because they can change how agents behave in your environment.

Before installing, confirm:
- What commands or tools the skill may invoke
- What files or directories it can affect
- Whether it relies on external services, network access, or third-party content
- Whether the scope and permissions are acceptable for your project

## Skills

| Skill | Description |
|---|---|
| **cleanup-review** | Structural code review for redundancy, dead code, and convention violations. Read-only by default. |
| **subagent-collab** | Delegate subtasks to other coding agents (Claude Code, Codex, Cursor) via CLI subprocesses. |
| **release-notes** | Generate release notes from git history. Produces a technical changelog with commit references and a user-facing changelog in plain language. |

## Installation

All platforms use the same `SKILL.md` format. The differences are where the files go, and whether the platform has a built-in installer or marketplace.

### Claude Code

Claude Code has a **plugin marketplace** system for discovering and installing skills. This is the recommended approach. Manual file copy is also supported.

**Marketplace (recommended):**

Skills distributed via marketplace are installed directly from within a Claude Code session:

```bash
# Register a marketplace (GitHub repo with a marketplace.json)
/plugin marketplace add anthropics/skills

# Install a specific skill/plugin from the marketplace
/plugin install document-skills@anthropic-agent-skills
```

Anthropic maintains an official marketplace at [`anthropics/skills`](https://github.com/anthropics/skills) and [`anthropics/claude-code`](https://github.com/anthropics/claude-code). Community marketplaces are GitHub repos containing a `.claude-plugin/marketplace.json` catalog — anyone can create and host one. Browse and install interactively with the `/plugin` menu.

Useful marketplace commands:
```bash
/plugin marketplace list       # List registered marketplaces
/plugin marketplace update     # Refresh marketplace catalogs
/plugin list                   # List installed plugins
/plugin uninstall <name>       # Remove a plugin
```

**Manual install (standalone skills without a marketplace):**

Claude Code discovers skills from `~/.claude/skills/` (global) and `.claude/skills/` (project-level). Project skills take precedence.

```bash
# Global (available in all projects)
mkdir -p ~/.claude/skills
cp -r /path/to/skills/* ~/.claude/skills/

# Project (scoped to one repo)
cd your-project
mkdir -p .claude/skills
cp -r /path/to/skills/* .claude/skills/
```

Manually installed skills are available immediately. Claude auto-discovers them and can invoke them by name or load them automatically when relevant.

Docs: [Claude Code Plugins](https://code.claude.com/docs/en/plugin-marketplaces) · [Claude Code Skills](https://code.claude.com/docs/en/skills)

---

### Cursor

Cursor supports the Agent Skills standard as of January 2026. There is **no built-in marketplace or installer** — installation is manual file copy only.

Cursor discovers skills from these locations:
- `.cursor/skills/` — project-level
- `.claude/skills/` — project-level (Claude Code compatibility)
- `~/.cursor/skills/` — user-level (global)

> **Note:** `~/.cursor/skills-cursor/` is reserved for Cursor's built-in skills (e.g. `create-skill`, `create-rule`). Do not install custom skills there.

**Global install:**
```bash
mkdir -p ~/.cursor/skills
cp -r /path/to/skills/* ~/.cursor/skills/
```

**Project install:**
```bash
cd your-project
mkdir -p .cursor/skills
cp -r /path/to/skills/* .cursor/skills/
```

Skills are discovered automatically. The agent matches your request to a skill's description and activates it when relevant.

Because Cursor also reads `.claude/skills/`, projects that already have skills installed for Claude Code will work in Cursor without any extra setup.

Docs: [Cursor Docs](https://docs.cursor.com)

---

### Codex CLI

Codex CLI has a **built-in skill installer** and an official skills catalog hosted at [`openai/skills`](https://github.com/openai/skills). The catalog is organized into three tiers:

- `.system` — automatically installed with Codex
- `.curated` — vetted skills, installable by name
- `.experimental` — community/preview skills, installable by folder path or URL

**Install via the built-in installer (inside a Codex session):**
```bash
# Install a curated skill by name
$skill-installer install cleanup-review

# Install an experimental skill by path or URL
$skill-installer install https://github.com/openai/skills/tree/main/skills/.experimental/create-plan
```

Restart Codex after installing new skills if they don't appear automatically.

**Manual install (standalone skills):**

Codex discovers skills from `~/.codex/skills/` (global) and `.agents/skills/` (project-level, scanned from working directory up to repo root).

```bash
# Global
mkdir -p ~/.codex/skills
cp -r /path/to/skills/* ~/.codex/skills/

# Project
cd your-project
mkdir -p .agents/skills
cp -r /path/to/skills/* .agents/skills/
```

Invoke explicitly with `/skills` or `$skill-name` in your prompt, or let Codex match implicitly by description.

To disable a skill without deleting it, add to `~/.codex/config.toml`:
```toml
[[skills.config]]
path = "/path/to/skill/SKILL.md"
enabled = false
```

Docs: [Codex Agent Skills](https://developers.openai.com/codex/skills/) · [Codex Changelog](https://developers.openai.com/codex/changelog)

---

### Gemini CLI

Gemini CLI has **built-in skill management commands** and supports skill distribution through **extensions**. Skills can also be installed manually.

Gemini CLI discovers skills from three tiers (highest precedence first):
1. `.gemini/skills/` — workspace-scoped
2. `~/.gemini/skills/` — user-scoped
3. Extension-bundled skills — installed via `gemini extensions install`

Workspace skills override user skills when names collide.

**Install via CLI commands:**

Skills can be packaged into `.skill` files and installed with scope control:
```bash
# Install to current workspace
gemini skills install <path/to/skill-name.skill> --scope workspace

# Install for all workspaces
gemini skills install <path/to/skill-name.skill> --scope user
```

**Install via extensions:**

Extensions bundle skills alongside MCP servers, commands, and context. Install from a GitHub URL:
```bash
gemini extensions install https://github.com/<owner>/<extension-repo>
```

**In-session management:**
```bash
/skills list                          # List discovered skills
/skills enable cleanup-review         # Enable a skill
/skills disable subagent-collab       # Disable a skill
/skills reload                        # Reload after installing new skills
```

> **Note:** `/skills enable` and `/skills disable` default to user scope. Use `--scope workspace` for workspace-specific settings.

**Manual install (standalone skills):**
```bash
# User-scoped
mkdir -p ~/.gemini/skills
cp -r /path/to/skills/* ~/.gemini/skills/

# Workspace-scoped
cd your-project
mkdir -p .gemini/skills
cp -r /path/to/skills/* .gemini/skills/
```

Gemini auto-discovers skills at session start. When it matches your request to a skill, it calls `activate_skill` and asks for confirmation before loading the full instructions into context.

Docs: [Gemini CLI Agent Skills](https://geminicli.com/docs/cli/skills/) · [Getting Started with Skills](https://geminicli.com/docs/cli/tutorials/skills-getting-started/)

---

### Cross-platform installers

If you use multiple platforms, these third-party tools can install skills across all of them in one step:

**Vercel's `add-skill`:**
```bash
bunx add-skill <owner>/<repo>
```

**OpenSkills:**
```bash
npx openskills install <owner>/<repo>
npx openskills sync
```

Both tools detect which platforms are present and write to the correct directories automatically.

### Symlink for shared installs

If you prefer manual control, copy skills once and symlink the directory:

```bash
# Copy all skills to one canonical location
mkdir -p ~/.claude/skills
cp -r /path/to/skills/* ~/.claude/skills/

# Symlink the directory for other platforms
ln -s ~/.claude/skills ~/.cursor/skills
ln -s ~/.claude/skills ~/.codex/skills
ln -s ~/.claude/skills ~/.gemini/skills
```

For per-skill control, symlink each skill instead: `for skill in ~/.claude/skills/*; do name=$(basename "$skill"); ln -sf "$skill" ~/.cursor/skills/"$name"; done` (repeat for each platform).

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

The `name` becomes the slash command (e.g., `/cleanup-review`). The `description` is what agents use to decide when to load the skill automatically — keep it precise.

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

| Platform | Marketplace / Installer | Global path | Project path | Docs |
|---|---|---|---|---|
| Claude Code | `/plugin marketplace add` + `/plugin install` | `~/.claude/skills/` | `.claude/skills/` | [code.claude.com](https://code.claude.com/docs/en/skills) |
| Cursor | None (manual copy only) | `~/.cursor/skills/` | `.cursor/skills/` | [cursor.com](https://docs.cursor.com) |
| Codex CLI | `$skill-installer install` (in-session) | `~/.codex/skills/` | `.agents/skills/` | [developers.openai.com](https://developers.openai.com/codex/skills/) |
| Gemini CLI | `gemini skills install` + extensions | `~/.gemini/skills/` | `.gemini/skills/` | [geminicli.com](https://geminicli.com/docs/cli/skills/) |

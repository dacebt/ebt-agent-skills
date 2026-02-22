---
name: subagent-interop
description: >
Reference for invoking provider CLIs as subprocess agents (Claude Code, Codex CLI, Cursor Agent, Gemini CLI).
Use to look up the command + flags for a chosen provider.
---
# Shadow Clone Protocol

[PROVIDER_SELECTION]
Prompt the user to choose a provider.

If a built-in user prompt UI/tool is available, use it to present a multi-select choice:

1. Codex CLI
2. Claude Code
3. Cursor Agent
4. Gemini CLI

[PROVIDERS]

Claude Code

* Binary: `claude`
* Invocation: `claude -p "<task>"`
* Flags:

  * `--model <name>`
  * `--permission-mode <mode>`
  * `--dangerously-skip-permissions`
  * `--allowedTools <tools...>`
  * `--disallowedTools <tools...>`
  * `--tools <tools...>`
  * `--output-format <fmt>`
  * `--json-schema <schema>`
  * `--max-budget-usd <n>`
  * `--append-system-prompt <prompt>`
  * `--add-dir <path>`
  * `--mcp-config <path>`
  * `--strict-mcp-config`
  * `--no-session-persistence`
  * `--fallback-model <name>`
  * `--effort <level>`

Codex CLI

* Binary: `codex`
* Invocation: `codex exec "<task>"`
* Full-auto: `codex exec --full-auto "<task>"`
* Flags:

  * `--model <name>`
  * `--sandbox <mode>`
  * `--full-auto`
  * `--dangerously-bypass-approvals-and-sandbox`
  * `--json`
  * `--output-schema <schema>`
  * `-o, --output-last-message <path>`
  * `--cd <path>`
  * `--add-dir <path>`
  * `--image <path>`
  * `--config <key=value>`
  * `--ephemeral`
  * `--skip-git-repo-check`

Cursor Agent

* Binary: `cursor-agent`
* Invocation: `cursor-agent chat "<task>"`
* Flags:

  * `--model <name>`
  * `-p, --print`
  * `--output-format <fmt>`
  * `--mode <mode>`
  * `--force` / `--yolo`
  * `--sandbox <mode>`
  * `--trust`
  * `--workspace <path>`
  * `--approve-mcps`

Gemini CLI

* Binary: `gemini`
* Invocation: `gemini -p "<task>"`
* Flags:

  * `--model <name>`
  * `-p, --prompt`
  * `--sandbox`
  * `-y, --yolo`
  * `--approval-mode <mode>`
  * `--allowed-tools <tools>`
  * `-o, --output-format <fmt>`
  * `--include-directories <dirs>`
  * `-e, --extensions <exts>`
  * `-r, --resume`

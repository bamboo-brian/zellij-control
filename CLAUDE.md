# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

Test the plugin locally (loads it for one session without installing):
```bash
claude --plugin-dir .
```

Reload changes during an active session:
```
/reload-plugins
```

Validate the plugin structure before publishing:
```bash
claude plugin validate
```

## Plugin Structure

```
zellij-control/
├── .claude-plugin/
│   └── plugin.json       # Plugin identity, name, version, metadata
├── skills/
│   └── <name>/
│       └── SKILL.md      # One subdirectory per skill
├── agents/               # Custom agent definitions (if needed)
├── hooks/
│   └── hooks.json        # Event handlers (PostToolUse, SessionStart, etc.)
├── .mcp.json             # MCP server configurations (if needed)
└── settings.json         # Default settings applied when plugin is enabled
```

**Critical layout rule:** `commands/`, `agents/`, `skills/`, and `hooks/` must sit at the plugin root — never inside `.claude-plugin/`. Only `plugin.json` goes inside `.claude-plugin/`.

## Skill Authoring

Skills live at `skills/<name>/SKILL.md`. The directory name becomes the invocation name, namespaced by the plugin: `/zellij-control:<name>`.

Minimal `SKILL.md` format:
```markdown
---
description: One-line description — Claude uses this to decide when to invoke the skill automatically.
---

Skill instructions here.
```

Use `$ARGUMENTS` to capture text the user passes after the skill name.

## Hooks

Hooks live in `hooks/hooks.json` and fire on Claude Code lifecycle events (`PreToolUse`, `PostToolUse`, `SessionStart`, `Stop`). The hook command receives JSON on stdin; use `jq` to extract fields.

## Testing Skills

After loading with `--plugin-dir .`, invoke a skill as:
```
/zellij-control:<skill-name> [optional args]
```

Check `/help` to see all skills listed under the plugin namespace.

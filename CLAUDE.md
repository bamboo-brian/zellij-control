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
├── bin/
│   └── zj                # Wrapper around `zellij` — skills call this, never `zellij` directly
├── skills/
│   └── <name>/
│       └── SKILL.md      # One subdirectory per skill
├── agents/               # Custom agent definitions (if needed)
├── hooks/
│   └── hooks.json        # Event handlers (PostToolUse, SessionStart, etc.)
├── .mcp.json             # MCP server configurations (if needed)
└── settings.json         # Default settings applied when plugin is enabled
```

## The `zj` wrapper — do not call `zellij` directly

**All skills invoke `zj` (in `bin/`), never the `zellij` binary directly.**

When Claude Code runs, it exports marker environment variables (`CLAUDE_*`,
`CLAUDECODE`, `AI_AGENT`). A Zellij *session* inherits the environment of the
process that first started its server, and every pane and command that session
later spawns inherits that same environment. If those markers leak into a
session, any `claude` started inside one of its panes sees them, believes it is
a nested child session, and refuses to persist its own conversation history.

`bin/zj` is a drop-in `zellij` replacement that unsets every `CLAUDE*` /
`AI_AGENT` variable and then `exec`s the real `zellij` with all arguments
passed through. Routing every skill through this single choke point guarantees
spawned sessions and panes start with a clean environment.

Claude Code automatically adds each installed plugin's `bin/` directory to
`PATH`, so skills can call `zj` as a bare command. `bin/zj` must stay
executable (`chmod +x`); the executable bit is tracked in git.

When adding or editing a skill, write `zj` in every command — never `zellij`.
The only legitimate `zellij` references are non-command ones: the product name
in prose, plugin locations like `zellij:status-bar` in layout KDL, and the
`zellij.dev` documentation URL.

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

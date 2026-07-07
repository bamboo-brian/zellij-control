---
description: Overview of Zellij sessions, tabs, and panes. With no arguments, shows the current session's tabs and panes. Pass a session name to inspect another session. Pass "sessions" (or "--sessions") to list all active sessions.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` controls behavior:

**List all sessions** (arguments is `sessions` or `--sessions`):
```bash
zj list-sessions -n
```
Present each session name, age, and whether it is the current one.

**Current session** (no arguments):
```bash
zj action list-tabs --json
zj action list-panes --json
```

**Named session** (arguments is a session name):
```bash
zj --session $ARGUMENTS action list-tabs --json
zj --session $ARGUMENTS action list-panes --json
```

For session tab/pane output: parse and present as a tree, grouped by tab. For each tab show its ID, name, and whether it is active. Under each tab, list every pane with:
- pane ID (e.g. `terminal_1`) — needed for future commands
- whether the pane is focused
- pane title or running command
- working directory

Present this as a compact, readable summary. Do not dump raw JSON.

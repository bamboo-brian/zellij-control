---
description: Overview of Zellij sessions, tabs, and panes. With no arguments, shows the current session's tabs and panes. Pass a session name to inspect another session. Pass "sessions" (or "--sessions") to list all active sessions.
---

`$ARGUMENTS` controls behavior:

**List all sessions** (arguments is `sessions` or `--sessions`):
```bash
zellij list-sessions -n
```
Present each session name, age, and whether it is the current one.

**Current session** (no arguments):
```bash
zellij action list-tabs --json
zellij action list-panes --json
```

**Named session** (arguments is a session name):
```bash
zellij --session $ARGUMENTS action list-tabs --json
zellij --session $ARGUMENTS action list-panes --json
```

For session tab/pane output: parse and present as a tree, grouped by tab. For each tab show its ID, name, and whether it is active. Under each tab, list every pane with:
- pane ID (e.g. `terminal_1`) — needed for future commands
- whether the pane is focused
- pane title or running command
- working directory

Present this as a compact, readable summary. Do not dump raw JSON.

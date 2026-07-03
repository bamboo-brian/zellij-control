---
description: Read the full content (viewport + scrollback) of a specific Zellij pane by its ID. Use session-overview first to find pane IDs. Usage: /zellij-control:read-pane <pane-id> [session-name]
---

`$ARGUMENTS` is `<pane-id>` optionally followed by a session name (e.g. `terminal_1` or `terminal_1 my-session`). Parse accordingly.

**Current session** (no session name given):
```bash
zellij action dump-screen --pane-id <pane-id> --full
```

**Named session** (session name provided):
```bash
zellij --session <session-name> action dump-screen --pane-id <pane-id> --full
```

Present the output as-is — it is the rendered terminal content of that pane. If the pane ID is invalid or the command fails, report the error.

Note: panes in inactive (background) tabs are not rendered by Zellij and will return empty output. If a pane returns nothing, use `manage-panes` to focus it first (`focus-pane-id`), then re-run this skill. Be aware that focusing will switch the visible tab in the session.

Tip: if the pane has a long history and you only want the output of the most recent command, clear its scrollback first with `zellij action clear -p <pane-id>` before running the command, then read afterwards.

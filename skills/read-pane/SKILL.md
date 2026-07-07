---
description: "Read the full content (viewport + scrollback) of a specific Zellij pane by its ID. Use session-overview first to find pane IDs. Usage: /zellij-control:read-pane <pane-id> [session-name]"
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` is `<pane-id>` optionally followed by a session name (e.g. `terminal_1` or `terminal_1 my-session`). Parse accordingly.

**Current session** (no session name given):
```bash
zj action dump-screen --pane-id <pane-id> --full
```

**Named session** (session name provided):
```bash
zj --session <session-name> action dump-screen --pane-id <pane-id> --full
```

Present the output as-is — it is the rendered terminal content of that pane. If the pane ID is invalid or the command fails, report the error.

Note: panes in inactive (background) tabs are not rendered by Zellij and will return empty output. If a pane returns nothing, use `manage-panes` to focus it first (`focus-pane-id`), then re-run this skill. Be aware that focusing will switch the visible tab in the session.

Tip: if the pane has a long history and you only want the output of the most recent command, clear its scrollback first with `zj action clear -p <pane-id>` before running the command, then read afterwards.

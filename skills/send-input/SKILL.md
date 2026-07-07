---
description: Send text or keystrokes to a specific Zellij pane, or broadcast input to every pane in a tab. Use session-overview first to find pane IDs. Optionally target a different session by including its name.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` describes what to send and where. Parse the pane ID, optional session name, and the input to send.

All commands accept an optional `zj --session <name>` prefix to target a different session.

## Sending text

Use `paste` for sending text — it uses bracketed paste mode which handles spaces, multiline, and special characters correctly:
```bash
zj action paste -p <pane-id> "text to send"
```

To send text and immediately run it, follow with an Enter key:
```bash
zj action paste -p <pane-id> "some command"
zj action send-keys -p <pane-id> "Enter"
```

## Sending special keys

Use `send-keys` for keys that aren't printable text:
```bash
zj action send-keys -p <pane-id> "Enter"
zj action send-keys -p <pane-id> "Esc"
zj action send-keys -p <pane-id> "Ctrl c"
zj action send-keys -p <pane-id> "Ctrl d"
zj action send-keys -p <pane-id> "Up"
zj action send-keys -p <pane-id> "Down"
zj action send-keys -p <pane-id> "Tab"
```

Multiple keys can be sent in one call (space-separated):
```bash
zj action send-keys -p <pane-id> "Up" "Up" "Enter"
```

## Writing raw characters or bytes

`write-chars` sends a string as-is (no bracketed paste); `write` sends raw byte values. Prefer `paste` for normal text — reach for these only when you specifically need un-bracketed input or exact bytes:
```bash
zj action write-chars -p <pane-id> "text"
zj action write -p <pane-id> 27 91 65        # raw bytes (e.g. an escape sequence)
```

## Broadcasting to every pane in a tab

Toggle input sync so keystrokes go to all panes in a tab at once (toggle again to stop):
```bash
zj action toggle-active-sync-tab                 # active tab
zj action toggle-active-sync-tab -t <tab-id>     # specific tab
```
While sync is on, `send-keys`/`write-chars` typed into any pane are mirrored to all panes in that tab. Turn it back off when done so later input isn't broadcast unintentionally.

## Notes

- Prefer `paste` over `write-chars` for any text longer than a single character — it handles edge cases more reliably.
- If the target pane is in an inactive tab, use `manage-panes` to focus it first, otherwise input may not be received.
- After sending a command, use `read-pane` to check the output.

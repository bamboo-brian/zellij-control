---
description: Send text or keystrokes to a specific Zellij pane. Use session-overview first to find pane IDs. Optionally target a different session by including its name.
---

`$ARGUMENTS` describes what to send and where. Parse the pane ID, optional session name, and the input to send.

All commands accept an optional `zellij --session <name>` prefix to target a different session.

## Sending text

Use `paste` for sending text — it uses bracketed paste mode which handles spaces, multiline, and special characters correctly:
```bash
zellij action paste -p <pane-id> "text to send"
```

To send text and immediately run it, follow with an Enter key:
```bash
zellij action paste -p <pane-id> "some command"
zellij action send-keys -p <pane-id> "Enter"
```

## Sending special keys

Use `send-keys` for keys that aren't printable text:
```bash
zellij action send-keys -p <pane-id> "Enter"
zellij action send-keys -p <pane-id> "Esc"
zellij action send-keys -p <pane-id> "Ctrl c"
zellij action send-keys -p <pane-id> "Ctrl d"
zellij action send-keys -p <pane-id> "Up"
zellij action send-keys -p <pane-id> "Down"
zellij action send-keys -p <pane-id> "Tab"
```

Multiple keys can be sent in one call (space-separated):
```bash
zellij action send-keys -p <pane-id> "Up" "Up" "Enter"
```

## Notes

- Prefer `paste` over `write-chars` for any text longer than a single character — it handles edge cases more reliably.
- If the target pane is in an inactive tab, use `manage-panes` to focus it first, otherwise input may not be received.
- After sending a command, use `read-pane` to check the output.

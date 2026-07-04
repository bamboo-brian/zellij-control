---
description: Manage the lifecycle of Zellij sessions — rename, switch, save (persist state), detach, kill, or delete sessions. Use new-session to create one and session-overview to list them.
---

`$ARGUMENTS` describes what to do. Parse the intent and run the appropriate command(s) below. The `action` subcommands accept an optional `zellij --session <name>` prefix to target a different session; the top-level `kill-session`/`delete-session` commands take the session name as an argument instead.

## Rename

Rename the current session:
```bash
zellij action rename-session <new-name>
```

## Switch

Switch the attached client to another session (creating it if it doesn't exist):
```bash
zellij action switch-session <session-name>
zellij action switch-session <session-name> --cwd <path>
zellij action switch-session <session-name> --layout <path/to/layout.kdl>
zellij action switch-session <session-name> --pane-id <pane-id>   # focus a pane in the target
```

## Save

Immediately persist the session's state to disk (for later resurrection):
```bash
zellij action save-session
```

## Detach

Detach from the current session, leaving it running in the background:
```bash
zellij action detach
```

## Kill vs. delete

`kill-session` stops a running session but keeps it on disk so it can be resurrected. `delete-session` removes it from disk entirely; add `--force` to kill it first if it's still running.

```bash
zellij kill-session <session-name>        # stop a running session (resurrectable)
zellij kill-all-sessions                  # stop all running sessions
zellij delete-session <session-name>      # remove an exited session from disk
zellij delete-session --force <session-name>   # kill if running, then delete
zellij delete-all-sessions                # remove all exited sessions
```

Use `session-overview sessions` to list sessions (including exited ones) before killing or deleting.

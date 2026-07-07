---
description: Manage the lifecycle of Zellij sessions — rename, switch, save (persist state), detach, kill, or delete sessions. Use new-session to create one and session-overview to list them.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` describes what to do. Parse the intent and run the appropriate command(s) below. The `action` subcommands accept an optional `zj --session <name>` prefix to target a different session; the top-level `kill-session`/`delete-session` commands take the session name as an argument instead.

## Rename

Rename the current session:
```bash
zj action rename-session <new-name>
```

## Switch

Switch the attached client to another session (creating it if it doesn't exist):
```bash
zj action switch-session <session-name>
zj action switch-session <session-name> --cwd <path>
zj action switch-session <session-name> --layout <path/to/layout.kdl>
zj action switch-session <session-name> --pane-id <pane-id>   # focus a pane in the target
```

## Save

Immediately persist the session's state to disk (for later resurrection):
```bash
zj action save-session
```

## Detach

Detach from the current session, leaving it running in the background:
```bash
zj action detach
```

## Kill vs. delete

`kill-session` stops a running session but keeps it on disk so it can be resurrected. `delete-session` removes it from disk entirely; add `--force` to kill it first if it's still running.

```bash
zj kill-session <session-name>        # stop a running session (resurrectable)
zj kill-all-sessions                  # stop all running sessions
zj delete-session <session-name>      # remove an exited session from disk
zj delete-session --force <session-name>   # kill if running, then delete
zj delete-all-sessions                # remove all exited sessions
```

Use `session-overview sessions` to list sessions (including exited ones) before killing or deleting.

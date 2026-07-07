---
description: Create a new detached Zellij session running in the background. Optionally specify a layout, working directory, or shell. The session can then be targeted by other skills using its name.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`. This matters most here: the session's server inherits this environment and passes it to every pane it ever spawns.

`$ARGUMENTS` is the session name, optionally followed by additional options.

## Basic usage

Create a background session with a given name (no-op if it already exists):
```bash
zj attach --create-background <session-name>
```

## With a layout

```bash
zj attach --create-background <session-name> options --default-layout <path/to/layout.kdl>
```

Use the `layout` skill to dump an existing session's layout to a file first if you want to replicate it.

## With a working directory or shell

```bash
zj attach --create-background <session-name> options --default-cwd <path>
zj attach --create-background <session-name> options --default-shell <shell>
```

Options can be combined:
```bash
zj attach --create-background <session-name> options --default-layout <layout.kdl> --default-cwd <path>
```

## Notes

- If a session with the given name already exists, the command does nothing (idempotent).
- The session runs detached — no terminal is attached to it. Use `session-overview` with the session name to inspect it, and other skills with `--session <name>` to interact with its panes.
- To list all sessions including the new one, use `session-overview sessions`.

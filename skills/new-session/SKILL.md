---
description: Create a new detached Zellij session running in the background. Optionally specify a layout, working directory, or shell. The session can then be targeted by other skills using its name.
---

`$ARGUMENTS` is the session name, optionally followed by additional options.

## Basic usage

Create a background session with a given name (no-op if it already exists):
```bash
zellij attach --create-background <session-name>
```

## With a layout

```bash
zellij attach --create-background <session-name> options --default-layout <path/to/layout.kdl>
```

Use the `layout` skill to dump an existing session's layout to a file first if you want to replicate it.

## With a working directory or shell

```bash
zellij attach --create-background <session-name> options --default-cwd <path>
zellij attach --create-background <session-name> options --default-shell <shell>
```

Options can be combined:
```bash
zellij attach --create-background <session-name> options --default-layout <layout.kdl> --default-cwd <path>
```

## Notes

- If a session with the given name already exists, the command does nothing (idempotent).
- The session runs detached — no terminal is attached to it. Use `session-overview` with the session name to inspect it, and other skills with `--session <name>` to interact with its panes.
- To list all sessions including the new one, use `session-overview sessions`.

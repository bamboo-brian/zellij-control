---
description: Run a shell command in a new Zellij pane. Supports blocking until the command finishes (and capturing exit status) or running in the background and watching with watch-pane. Optionally target a different session.
---

`$ARGUMENTS` describes the command to run and any options (floating, named, working directory, session, blocking behaviour). All commands accept an optional `zellij --session <name>` prefix.

## Blocking run (preferred for agents)

Runs the command in a new pane and blocks until it exits, returning the exit code. Use this when you need to know the result before continuing.

```bash
# Block until exit (any status) — returns exit code of the command
zellij action new-pane --block-until-exit -- <command>

# Block and only succeed when exit code is 0
zellij action new-pane --block-until-exit-success -- <command>

# Block and only succeed on non-zero exit
zellij action new-pane --block-until-exit-failure -- <command>
```

Blocking variants do not return a pane ID — stdout is used for the exit code. If you only need to know whether the command succeeded and don't need to read its output, blocking is sufficient:

```bash
zellij action new-pane --block-until-exit -- <command>
EXIT=$?
```

To capture output as well, run non-blocking to get the pane ID, then use `watch-pane` to detect when the pane closes (`CLOSED: terminal_N`), then `read-pane` for the output.

## Non-blocking run

Starts the command and returns the pane ID immediately. Use `watch-pane` to stream output or `read-pane` to snapshot it later.

```bash
PANE_ID=$(zellij action new-pane -- <command>)
```

## Options

These can be combined with any blocking variant above:

```bash
zellij action new-pane -f -- <command>              # floating pane
zellij action new-pane -n <name> -- <command>       # named pane
zellij action new-pane --cwd <path> -- <command>    # set working directory
zellij action new-pane --close-on-exit -- <command> # auto-close pane when done
zellij action new-pane -d right -- <command>        # split direction: right|down
```

## Clearing a pane before use

If reusing an existing pane (via `send-input`) rather than opening a new one, clear its scrollback first so `read-pane` returns only the new output:
```bash
zellij action clear -p <pane-id>
```

## Pane lifecycle

By default Zellij holds a pane open after the command exits so the output remains readable — it does **not** auto-close. Always explicitly clean up with `manage-panes` (`close-pane -p <pane-id>`) once you're done reading output.

To auto-close immediately on exit (when you don't need to read output afterwards):
```bash
zellij action new-pane --close-on-exit -- <command>
```

## Typical agent workflow

1. Run non-blocking, capture pane ID
2. Use `watch-pane` to detect completion (waits for `CLOSED` event if using `--close-on-exit`, or monitors output otherwise)
3. Use `read-pane <pane-id>` to get the full output
4. Close the pane with `manage-panes` (`close-pane -p <pane-id>`)

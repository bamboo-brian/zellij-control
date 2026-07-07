---
description: Create, close, focus (by id or direction), rename, resize, move, stack, fullscreen, restyle, and toggle floating Zellij panes and tabs. Use session-overview first to find pane and tab IDs. Optionally pass a session name to operate on a different session.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` describes what to do. Parse the intent and run the appropriate command(s) below. All commands accept an optional `zj --session <name>` prefix to target a different session.

## Focusing

Focus a specific pane (switches to its tab if needed):
```bash
zj action focus-pane-id <pane-id>
```

Move focus directionally or cycle through panes:
```bash
zj action move-focus <direction>          # direction: left|right|up|down
zj action move-focus-or-tab <direction>   # crosses tab boundaries at the edge
zj action focus-next-pane
zj action focus-previous-pane
```

Switch to a tab:
```bash
zj action go-to-tab-by-id <tab-id>
zj action go-to-tab-name <tab-name>
zj action go-to-tab <tab-index>
zj action go-to-next-tab
zj action go-to-previous-tab
```

Toggle fullscreen for the focused pane (or a specific one):
```bash
zj action toggle-fullscreen
zj action toggle-fullscreen -p <pane-id>
```

## Creating

New tiled pane (returns the new pane ID):
```bash
zj action new-pane                        # auto-placed
zj action new-pane -d right               # split right
zj action new-pane -d down                # split down
zj action new-pane -n <name>              # with a name
zj action new-pane --cwd <path>           # set working directory
zj action new-pane -- <command>           # run a command in it
```

New floating pane:
```bash
zj action new-pane -f
zj action new-pane -f --x 10% --y 10% --width 80% --height 80%
```

New tab (returns the new tab ID):
```bash
zj action new-tab
zj action new-tab -n <name>
zj action new-tab --cwd <path>
zj action new-tab -- <command>
```

## Renaming

Rename a pane (defaults to the focused pane; use `--pane-id` to target another):
```bash
zj action rename-pane "<new name>"
zj action rename-pane --pane-id <pane-id> "<new name>"
```

Rename a tab (defaults to the active tab; use `--tab-id` or `rename-tab-by-id` to target another):
```bash
zj action rename-tab "<new name>"
zj action rename-tab --tab-id <tab-id> "<new name>"
zj action rename-tab-by-id <tab-id> "<new name>"
```

Clear a previously set name (reverts to the default title):
```bash
zj action undo-rename-pane                  # focused pane
zj action undo-rename-pane --pane-id <pane-id>
zj action undo-rename-tab                   # active tab
zj action undo-rename-tab --tab-id <tab-id>
```

## Closing

Close a pane:
```bash
zj action close-pane -p <pane-id>
```

Close a tab:
```bash
zj action close-tab-by-id <tab-id>
```

## Resizing

Resize a pane at a given border:
```bash
zj action resize increase <direction> -p <pane-id>   # direction: left|right|up|down
zj action resize decrease <direction> -p <pane-id>
```

## Moving

Move a tiled pane in a direction (swaps with neighbour):
```bash
zj action move-pane <direction> -p <pane-id>         # direction: left|right|up|down
zj action move-pane-backwards -p <pane-id>           # rotate to the previous position
```

Combine several panes into a stack:
```bash
zj action stack-panes -- <pane-id> <pane-id> [<pane-id>...]
```

Float or embed a pane:
```bash
zj action toggle-pane-embed-or-floating -p <pane-id>  # not yet available; use focus first then toggle
```

Reposition or resize a floating pane:
```bash
zj action change-floating-pane-coordinates -p <pane-id> --x <x> --y <y> --width <w> --height <h>
```

Move a tab left or right:
```bash
zj action move-tab <direction>                        # direction: left|right
```

## Floating pane visibility

Toggle all floating panes in the current tab (show if hidden, hide if visible):
```bash
zj action toggle-floating-panes
```

Show or hide explicitly:
```bash
zj action show-floating-panes
zj action hide-floating-panes
```

These operate on the active tab. To target a specific tab, switch to it first with `go-to-tab-by-id`.

## Appearance

Show or hide a pane's border/frame:
```bash
zj action set-pane-borderless -p <pane-id> -b true     # -b false to re-show the border
zj action toggle-pane-frames                           # toggles frames for all panes (UI-wide)
```

Set a pane's foreground/background colors (or reset to defaults):
```bash
zj action set-pane-color -p <pane-id> --fg "#00e000" --bg "#001a3a"
zj action set-pane-color -p <pane-id> --reset
```

Pin a floating pane so it stays on top:
```bash
zj action toggle-pane-pinned -p <pane-id>
```

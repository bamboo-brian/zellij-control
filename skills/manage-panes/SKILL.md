---
description: Create, close, focus (by id or direction), rename, resize, move, stack, fullscreen, restyle, and toggle floating Zellij panes and tabs. Use session-overview first to find pane and tab IDs. Optionally pass a session name to operate on a different session.
---

`$ARGUMENTS` describes what to do. Parse the intent and run the appropriate command(s) below. All commands accept an optional `zellij --session <name>` prefix to target a different session.

## Focusing

Focus a specific pane (switches to its tab if needed):
```bash
zellij action focus-pane-id <pane-id>
```

Move focus directionally or cycle through panes:
```bash
zellij action move-focus <direction>          # direction: left|right|up|down
zellij action move-focus-or-tab <direction>   # crosses tab boundaries at the edge
zellij action focus-next-pane
zellij action focus-previous-pane
```

Switch to a tab:
```bash
zellij action go-to-tab-by-id <tab-id>
zellij action go-to-tab-name <tab-name>
zellij action go-to-tab <tab-index>
zellij action go-to-next-tab
zellij action go-to-previous-tab
```

Toggle fullscreen for the focused pane (or a specific one):
```bash
zellij action toggle-fullscreen
zellij action toggle-fullscreen -p <pane-id>
```

## Creating

New tiled pane (returns the new pane ID):
```bash
zellij action new-pane                        # auto-placed
zellij action new-pane -d right               # split right
zellij action new-pane -d down                # split down
zellij action new-pane -n <name>              # with a name
zellij action new-pane --cwd <path>           # set working directory
zellij action new-pane -- <command>           # run a command in it
```

New floating pane:
```bash
zellij action new-pane -f
zellij action new-pane -f --x 10% --y 10% --width 80% --height 80%
```

New tab (returns the new tab ID):
```bash
zellij action new-tab
zellij action new-tab -n <name>
zellij action new-tab --cwd <path>
zellij action new-tab -- <command>
```

## Renaming

Rename a pane (defaults to the focused pane; use `--pane-id` to target another):
```bash
zellij action rename-pane "<new name>"
zellij action rename-pane --pane-id <pane-id> "<new name>"
```

Rename a tab (defaults to the active tab; use `--tab-id` or `rename-tab-by-id` to target another):
```bash
zellij action rename-tab "<new name>"
zellij action rename-tab --tab-id <tab-id> "<new name>"
zellij action rename-tab-by-id <tab-id> "<new name>"
```

Clear a previously set name (reverts to the default title):
```bash
zellij action undo-rename-pane                  # focused pane
zellij action undo-rename-pane --pane-id <pane-id>
zellij action undo-rename-tab                   # active tab
zellij action undo-rename-tab --tab-id <tab-id>
```

## Closing

Close a pane:
```bash
zellij action close-pane -p <pane-id>
```

Close a tab:
```bash
zellij action close-tab-by-id <tab-id>
```

## Resizing

Resize a pane at a given border:
```bash
zellij action resize increase <direction> -p <pane-id>   # direction: left|right|up|down
zellij action resize decrease <direction> -p <pane-id>
```

## Moving

Move a tiled pane in a direction (swaps with neighbour):
```bash
zellij action move-pane <direction> -p <pane-id>         # direction: left|right|up|down
zellij action move-pane-backwards -p <pane-id>           # rotate to the previous position
```

Combine several panes into a stack:
```bash
zellij action stack-panes -- <pane-id> <pane-id> [<pane-id>...]
```

Float or embed a pane:
```bash
zellij action toggle-pane-embed-or-floating -p <pane-id>  # not yet available; use focus first then toggle
```

Reposition or resize a floating pane:
```bash
zellij action change-floating-pane-coordinates -p <pane-id> --x <x> --y <y> --width <w> --height <h>
```

Move a tab left or right:
```bash
zellij action move-tab <direction>                        # direction: left|right
```

## Floating pane visibility

Toggle all floating panes in the current tab (show if hidden, hide if visible):
```bash
zellij action toggle-floating-panes
```

Show or hide explicitly:
```bash
zellij action show-floating-panes
zellij action hide-floating-panes
```

These operate on the active tab. To target a specific tab, switch to it first with `go-to-tab-by-id`.

## Appearance

Show or hide a pane's border/frame:
```bash
zellij action set-pane-borderless -p <pane-id> -b true     # -b false to re-show the border
zellij action toggle-pane-frames                           # toggles frames for all panes (UI-wide)
```

Set a pane's foreground/background colors (or reset to defaults):
```bash
zellij action set-pane-color -p <pane-id> --fg "#00e000" --bg "#001a3a"
zellij action set-pane-color -p <pane-id> --reset
```

Pin a floating pane so it stays on top:
```bash
zellij action toggle-pane-pinned -p <pane-id>
```

---
description: Create, close, focus, resize, move, and toggle floating Zellij panes and tabs. Use session-overview first to find pane and tab IDs. Optionally pass a session name to operate on a different session.
---

`$ARGUMENTS` describes what to do. Parse the intent and run the appropriate command(s) below. All commands accept an optional `zellij --session <name>` prefix to target a different session.

## Focusing

Focus a specific pane (switches to its tab if needed):
```bash
zellij action focus-pane-id <pane-id>
```

Switch to a tab:
```bash
zellij action go-to-tab-by-id <tab-id>
zellij action go-to-tab-name <tab-name>
zellij action go-to-tab <tab-index>
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

---
description: Dump the current Zellij session layout as KDL, save it to a file, or restore a layout from a file or KDL string. Useful for snapshotting and recreating workspace configurations.
---

`$ARGUMENTS` describes what to do — dump, save, or restore a layout. All commands accept an optional `zellij --session <name>` prefix to target a different session.

## Dump current layout

Print the full session layout as a KDL string:
```bash
zellij action dump-layout
```

The output includes all tabs, panes, floating panes, plugin panes, and the `new_tab_template`. Present it to the user or save it to a file.

## Save layout to a file

```bash
zellij action dump-layout > <path/to/layout.kdl>
```

## Restore a layout from a file

Apply a saved layout file to the active tab:
```bash
zellij action override-layout <path/to/layout.kdl>
```

Apply to the active tab only (ignores additional tabs in the layout file):
```bash
zellij action override-layout --apply-only-to-active-tab <path/to/layout.kdl>
```

## Restore a layout from a KDL string

Useful when the layout was previously dumped and stored as a string rather than a file:
```bash
zellij action override-layout --layout-string '<kdl string>'
```

## Restore options

By default `override-layout` replaces all panes. Use these flags to preserve existing content:
```bash
--retain-existing-terminal-panes   # keep terminal panes that don't fit the new layout
--retain-existing-plugin-panes     # keep plugin panes that don't fit the new layout
```

---
description: Create, edit, dump, save, or restore a Zellij layout. Author new layouts in KDL (tabs, panes, splits, commands, plugins, templates), snapshot the current session, or restore a layout from a file or KDL string.
---

`$ARGUMENTS` describes what to do — create, edit, dump, save, or restore a layout. All commands accept an optional `zellij --session <name>` prefix to target a different session.

## Creating or editing a layout

Layouts are written in KDL. **Before writing or modifying a layout, read [`references/layout-kdl.md`](references/layout-kdl.md)** in this skill directory — it documents the KDL syntax (panes, `split_direction`, sizing, commands, `cwd`, plugins, floating panes, and templates) with copyable examples.

Starting points:
```bash
zellij setup --dump-layout default > layout.kdl   # default skeleton to edit
zellij action dump-layout > layout.kdl            # snapshot the current session as a base
```

Then edit `layout.kdl` and apply it with `override-layout` (below), or launch a new session with it using the `new-session` skill (`--default-layout <layout.kdl>`).

For the complete, authoritative reference see <https://zellij.dev/documentation/creating-a-layout.html>.

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

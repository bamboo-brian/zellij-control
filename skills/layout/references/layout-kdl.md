# Zellij layout KDL reference

How to author and modify Zellij layouts in KDL. For the authoritative and complete
reference see <https://zellij.dev/documentation/creating-a-layout.html>.

Apply a finished layout with the `layout` skill (`zj action override-layout
<file.kdl>`) or launch a session with it via the `new-session` skill
(`--default-layout <file.kdl>`).

## Top-level structure

Everything nests under a single `layout` node. The building blocks are `pane`,
`tab`, `pane_template`, and `tab_template`.

```kdl
layout {
    pane
    pane
}
```

## Panes

A `pane` can be bare, take inline arguments, or use a block with child nodes.

```kdl
layout {
    pane // bare pane (a shell)
    pane command="htop" // inline argument
    pane {
        command "exa"
        cwd "/"
    }
}
```

## Splitting

Nest panes and control their arrangement with `split_direction`. Values are
`"vertical"` or `"horizontal"` (default is `horizontal`).

```kdl
layout {
    pane split_direction="vertical" {
        pane
        pane
    }
    pane {
        // omitted, defaults to horizontal
        pane
        pane
    }
}
```

## Sizing

Use `size` with a fixed number (rows/columns) or a percentage string.

```kdl
layout {
    pane size=5
    pane split_direction="vertical" {
        pane size="80%"
        pane size="20%"
    }
}
```

## Running commands

Set `command` and pass `args`. Optional: `close_on_exit` (close the pane when the
command exits) and `start_suspended` (wait for ENTER before running).

```kdl
layout {
    pane command="htop"
    pane command="tail" {
        args "-f" "/path/to/my/logfile"
    }
    pane command="bash" {
        args "-c" "tail -f /path/to/my/logfile"
    }
}
```

## Working directory

A global `cwd` applies to the whole layout. Relative `cwd` on a pane/tab composes
with it; an absolute path overrides it.

```kdl
layout {
    cwd "/home/aram/code/my-project"
    pane cwd="src" // resolves to /home/aram/code/my-project/src
    pane cwd="/tmp" // absolute path overrides global cwd
    tab cwd="there" {
        pane cwd="friend" // composes relative paths
    }
}
```

## Visual and behavior attributes

```kdl
layout {
    pane name="my awesome pane"
    pane borderless=true
    pane focus=true          // first focused pane wins
    pane default_fg="#00e000"
    pane default_bg="#001a3a"
}
```

- `name` — custom pane title.
- `borderless` — hide the pane frame.
- `focus` — pane focused on startup (the first one set wins).
- `default_fg` / `default_bg` — hex or RGB color.

## Editing files

Open a file directly in the editor with `edit`.

```kdl
layout {
    pane split_direction="vertical" {
        pane edit="./git_diff_side_a"
        pane edit="./git_diff_side_b"
    }
}
```

## Plugin panes

Load a Zellij plugin with `plugin location`. Common built-ins:
`zellij:status-bar`, `zellij:tab-bar`, `zellij:compact-bar`.

```kdl
layout {
    pane {
        plugin location="zellij:status-bar"
    }
}
```

## Stacked and expanded panes

Stack panes so only one is expanded at a time.

```kdl
layout {
    pane stacked=true {
        pane
        pane
        pane command="ls"
        pane expanded=true
        pane command="htop"
    }
}
```

## Tabs

A `tab` groups panes. Tabs support `split_direction`, `focus`, `name`, `cwd`, and
`hide_floating_panes`.

```kdl
layout {
    tab // single pane tab
    tab {
        pane
        pane
        pane
    }
    tab name="my third tab" split_direction="vertical" {
        pane
        pane
    }
}
```

## Floating panes

Position panes absolutely or by percentage inside `floating_panes`.

```kdl
layout {
    floating_panes {
        pane
        pane command="ls"
        pane {
            x 1
            y "10%"
            width 200
            height "50%"
        }
    }
}
```

## Templates

### Pane templates

Define reusable panes with `pane_template`, then use the name as a node.

```kdl
layout {
    pane_template name="htop" {
        command "htop"
    }
    pane_template name="htop-tree" {
        command "htop"
        args "--tree"
        borderless true
    }
    htop
    htop-tree
}
```

Templates can accept consumer arguments:

```kdl
layout {
    pane_template name="follow-log" command="tail"
    follow-log {
        args "-f" "/tmp/my-first-log"
    }
    follow-log {
        args "-f" "my-second-log"
        cwd "/tmp"
    }
}
```

Use `children` to make a template a logical container — consumer panes are
inserted where `children` appears:

```kdl
layout {
    pane_template name="vertical-sandwich" split_direction="vertical" {
        pane
        children
        pane
    }
    vertical-sandwich {
        pane command="htop"
    }
}
```

### Tab templates

`tab_template` works the same way for tabs:

```kdl
layout {
    tab_template name="ranger-on-the-side" {
        pane size=1 borderless=true {
            plugin location="zellij:compact-bar"
        }
        pane split_direction="vertical" {
            pane command="ranger" size="20%"
            children
        }
    }
    ranger-on-the-side name="my first tab" {
        pane
        pane
    }
}
```

### Default tab template

`default_tab_template` is applied automatically to every tab.

```kdl
layout {
    default_tab_template {
        pane size=1 borderless=true {
            plugin location="zellij:tab-bar"
        }
        children
        pane size=2 borderless=true {
            plugin location="zellij:status-bar"
        }
    }
    tab
    tab name="second tab"
}
```

## Quick start

Dump the default layout as a starting skeleton to edit:

```bash
zj setup --dump-layout default > /tmp/my-quickstart-layout-file.kdl
```

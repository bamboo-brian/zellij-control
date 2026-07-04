# zellij-control

A [Claude Code](https://claude.com/claude-code) plugin for driving the
[Zellij](https://zellij.dev) terminal multiplexer. It gives Claude a set of
skills to create and inspect sessions, arrange tabs and panes, author layouts,
send input, and read pane output — so an agent can operate a real terminal
workspace on your behalf.

## Requirements

- [Zellij](https://zellij.dev/documentation/installation.html) installed and on your `PATH`
- Claude Code

## Installation

Load the plugin for a single session without installing:

```bash
claude --plugin-dir /path/to/zellij-control
```

During an active session, reload changes with `/reload-plugins`.

## Skills

Skills are namespaced under the plugin: invoke them as `/zellij-control:<name>`.
Claude also invokes them automatically when a request matches their description.

| Skill | What it does |
| --- | --- |
| `new-session` | Create a detached background session, optionally with a layout, cwd, or shell. |
| `manage-session` | Session lifecycle: rename, switch, save, detach, kill, or delete sessions. |
| `session-overview` | List sessions, or show the tabs and panes of a session (find pane/tab IDs here). |
| `manage-panes` | Create, close, focus, rename, resize, move, stack, fullscreen, restyle, and float panes and tabs. |
| `layout` | Create, edit, dump, save, or restore layouts. Author new layouts in KDL. |
| `run-command` | Run a shell command in a new pane, blocking for its exit status or backgrounding it. |
| `send-input` | Send text or keystrokes to a pane, or broadcast input to every pane in a tab. |
| `read-pane` | Read the full content (viewport + scrollback) of a pane by ID. |
| `watch-pane` | Watch a pane in real time and stream its output as notifications. |

Most skills accept an optional session name (or a `zellij --session <name>`
prefix) to target a session other than the current one. A typical flow is to
run `session-overview` first to discover pane and tab IDs, then act on them.

### Layout authoring

The `layout` skill bundles a KDL reference at
[`skills/layout/references/layout-kdl.md`](skills/layout/references/layout-kdl.md)
covering panes, splits, sizing, commands, plugins, floating panes, and
templates, with a link to the
[upstream layout documentation](https://zellij.dev/documentation/creating-a-layout.html).

## Example

```
/zellij-control:new-session dev
/zellij-control:run-command dev -- npm test
/zellij-control:watch-pane <pane-id> dev
```

## Development

Validate the plugin structure:

```bash
claude plugin validate .
```

See [CLAUDE.md](CLAUDE.md) for plugin layout conventions and skill-authoring
notes.

## License

See the repository for license details.

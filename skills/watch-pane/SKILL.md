---
description: "Watch a Zellij pane in real time using zellij subscribe. Streams pane output as notifications via the Monitor tool. Use when waiting for a long-running command to finish or watching for specific output. Usage: /zellij-control:watch-pane <pane-id> [session-name]"
---

`$ARGUMENTS` is `<pane-id>` optionally followed by a session name. Parse accordingly.

Use the Monitor tool with the following command, piped through `jq` to emit one readable line per update rather than raw JSON:

**Current session:**
```
zellij subscribe --pane-id <pane-id> --format json | jq -r --unbuffered 'if .event == "pane_closed" then "CLOSED: \(.pane_id)" else (.viewport | last) end'
```

**Named session:**
```
zellij --session <session-name> subscribe --pane-id <pane-id> --format json | jq -r --unbuffered 'if .event == "pane_closed" then "CLOSED: \(.pane_id)" else (.viewport | last) end'
```

This emits the last line of the pane's viewport on each update — typically where new output appears — and a `CLOSED` message when the pane exits.

## Monitor parameters

- Set `description` to something specific, e.g. `"terminal_3 cargo build output"`
- Use `persistent: true` for open-ended watching (log tails, REPLs, servers)
- Use a `timeout_ms` when waiting for a command expected to finish within a known window
- Call TaskStop to end a persistent monitor early

## Watching for a specific condition

To stop automatically when a pattern is matched (e.g. a build finishes or a server is ready), filter with grep instead:

```
zellij subscribe --pane-id <pane-id> --format json | jq -r --unbuffered '.viewport | last' | grep --line-buffered -E "pattern|error|FAILED"
```

The monitor exits when grep's output ends (if using `-m 1` for a single match), signalling completion.

## Note on idle panes

This approach works best when the pane has active output (a running command, build, server, etc.). On idle shells, every prompt redraw or resize triggers a notification with just the prompt character — effectively noise. If the target pane is idle, either wait until a command is running before starting the monitor, or use `read-pane` for a one-shot snapshot instead.

## After watching

Once done, use `read-pane` to capture the full final output of the pane.

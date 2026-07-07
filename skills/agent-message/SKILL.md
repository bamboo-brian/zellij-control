---
description: Send a prompt message to a Claude or Codex agent running in another Zellij session. Discovers agent panes automatically if no session/pane is specified.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` format: `[session-name] [pane-id] <message>`

Parse heuristically:
- If the first token matches `terminal_N` (a pane ID pattern), treat it as the pane ID for the current session.
- If the first token doesn't look like a pane ID and appears to be a single word (no spaces), treat it as a session name.
- Everything after recognized session/pane tokens is the message.
- If no message text is detectable (only a session/pane was provided, or `$ARGUMENTS` is empty), ask the user what message to send before proceeding.

## Step 1 — Discover agent sessions

If no session or pane was specified, locate agent panes across all sessions:

```bash
zj list-sessions -n
```

For each session returned, inspect its panes:

```bash
zj --session <name> action list-panes --json
```

Parse the JSON and look for panes whose `title`, `command`, or `running_command` field contains `claude` or `codex` (case-insensitive). These are agent panes.

- If exactly one match is found, proceed with it.
- If multiple matches are found, present them in a compact list (session name, pane ID, title/command) and ask the user which one to target.
- If no matches are found, report that no Claude or Codex panes were detected and ask the user to specify the session and pane manually.

## Step 2 — Send the message

Once the target `<session-name>` and `<pane-id>` are confirmed:

```bash
# Paste the message using bracketed paste (handles spaces, newlines, special characters)
zj --session <session-name> action paste -p <pane-id> "<message>"

# Submit it
zj --session <session-name> action send-keys -p <pane-id> "Enter"
```

For the current session (no session name needed), omit the `--session` flag:

```bash
zj action paste -p <pane-id> "<message>"
zj action send-keys -p <pane-id> "Enter"
```

## Step 3 — Watch the response (optional)

After sending, ask the user whether to watch the agent's response. If yes, stream its pane output:

```bash
zj --session <session-name> subscribe --pane-id <pane-id> --format json \
  | jq -r --unbuffered 'if .event == "pane_closed" then "CLOSED: \(.pane_id)" else (.viewport | last) end'
```

Use the Monitor tool with a descriptive label (e.g. `"agent response in <session-name>/<pane-id>"`). Set `persistent: true` if the agent is expected to take a long time, or use a `timeout_ms` if you expect a quick reply.

Alternatively, use `read-pane` for a one-shot snapshot of the current pane state:

```bash
zj --session <session-name> action dump-screen --pane-id <pane-id> --full
```

## Notes

- Panes in inactive (background) tabs may not render. If `read-pane` returns empty output, use `manage-panes` to focus the pane first (`focus-pane-id`), then re-read.
- If the agent pane is busy processing a previous prompt, the new input will queue behind the current one — check with `read-pane` first to confirm it is idle at a prompt.
- Use `session-overview` with a session name to inspect that session's full tab/pane tree if you need more context about its state.

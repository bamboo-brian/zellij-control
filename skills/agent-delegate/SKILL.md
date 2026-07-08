---
description: Delegate work from the current conversation to a Claude agent in another Zellij session. Synthesizes the task, relevant local file paths, remote links (tickets, PRs, stories), and applicable skill paths into a complete delegation message. Use when handing off work to an agent in a different project session.
---

> **Always run `zj`, never `zellij` directly.** `zj` is this plugin's wrapper (shipped in the plugin's `bin/`, already on your `PATH`). It clears Claude Code's `CLAUDE_*` / `CLAUDECODE` environment variables before invoking Zellij, so they are not inherited by the sessions and panes it spawns — otherwise a `claude` launched inside one of those panes would think it is a child process and refuse to keep its own history. The `zj` in the commands below is deliberate; do not substitute `zellij`.

`$ARGUMENTS` format: `[project-name]`

- If a project name is provided, use it to find the matching session (fuzzy match on session name).
- If no project name is provided, list available sessions and ask which one to target.

## Step 1 — Synthesize the delegation message

Before discovering the target session, compose the delegation message from the current conversation context. The message must be self-contained: the receiving agent has no access to this conversation history.

Structure the message as:

```
## Task

<Clear, concrete description of the work to be done. Include all relevant decisions, constraints, and acceptance criteria discussed in this conversation. Be specific enough that the agent can start without asking follow-up questions.>

## Context

<Background the agent needs to understand why this work matters or how it fits into the larger picture. Omit if the task is self-evident.>

## Relevant files

<List any local file paths that are relevant — documentation, config files, source files, test files, etc. Use the paths as they exist in the project being targeted, not the current working directory. Omit this section if no local files are relevant.>

- `path/to/file.ext` — <one-line note on why it matters>

## Remote resources

<List any URLs or references to remote resources mentioned or implied in the conversation — Jira/Linear tickets, GitHub PRs, design docs, Confluence pages, Notion docs, Figma links, etc. Omit if none.>

- [Title or ID](url) — <one-line note>

## Suggested skill

<If the task maps to a known Claude Code skill, name it and give its full path so the agent can invoke it even without the plugin loaded. Format:>

Skill: `/zellij-control:<skill-name>`
Full path: `~/.claude/plugins/zellij-control/skills/<skill-name>/SKILL.md`

<Omit if no skill is clearly applicable.>
```

Guidelines for composing the message:
- **Files**: include only files that are genuinely relevant, not a broad dump of the project structure. Prefer the project's own documentation, specs, or directly related source files.
- **Remote resources**: include tickets, PRs, stories, or design docs explicitly mentioned in the conversation or clearly implied by the task (e.g., if the user said "work on the auth refactor ticket", find or include that reference).
- **Skill**: scan available skills for a match. If the task is a layout change, suggest `/zellij-control:layout`. If it is session management, suggest `/zellij-control:manage-session`. If it is unclear, omit rather than guess.
- Do not include filler sections — omit any section that has nothing useful to say.

## Step 2 — Discover the target session

```bash
zj list-sessions -n
```

Match the provided project name (case-insensitive, partial match) against the session list. Prefer sessions whose name contains the project name over exact matches.

- If exactly one session matches, proceed with it.
- If multiple sessions match, present a compact list and ask the user which one to target.
- If no sessions match, list all sessions and ask the user to pick one or create a new session first with `/zellij-control:new-session`.

## Step 3 — Find the agent pane in the target session

```bash
zj --session <name> action list-panes --json
```

Parse the JSON and look for panes whose `title`, `command`, or `running_command` field contains `claude` or `codex` (case-insensitive).

- If exactly one agent pane is found, proceed.
- If multiple are found, present them and ask which one to target.
- If none are found, report that no agent pane was found in the session and ask the user to confirm the session or specify a pane ID.

## Step 4 — Show the message and confirm

Display the composed delegation message to the user and ask: **"Send this to `<session-name>/<pane-id>`?"**

Allow the user to:
- Confirm and send.
- Edit specific sections before sending.
- Cancel.

## Step 5 — Send the message

Once confirmed:

```bash
# Paste the message using bracketed paste (handles spaces, newlines, special characters)
zj --session <session-name> action paste -p <pane-id> "<message>"

# Submit it
zj --session <session-name> action send-keys -p <pane-id> "Enter"
```

## Step 6 — Watch the response (optional)

After sending, ask the user whether to watch the agent's response. If yes, use the Monitor tool to stream the pane or do a one-shot read:

```bash
zj --session <session-name> action dump-screen --pane-id <pane-id> --full
```

## Notes

- Check that the agent pane is idle before sending. If it appears busy (last line is not a prompt), warn the user — the new message will queue behind the current task.
- If the agent pane is in a background tab, it may not render. Use `manage-panes` to focus it first if `read-pane` returns empty output.
- The goal of this skill is to hand off work cleanly: the receiving agent should be able to act immediately without needing to ask follow-up questions.

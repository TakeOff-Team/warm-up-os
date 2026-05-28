# .claude/hooks/

Lifecycle automations — scripts that fire on Claude Code events (SessionStart, PreToolUse, PostToolUse, Stop, UserPromptSubmit).

Hooks are how Warm-Up-OS enforces guardrails and removes repetitive ceremony. The active config lives in `.claude/settings.json`.

---

## What hooks CAN do

- **PreToolUse hooks** can block a tool call before it runs, based on:
  - Tool name (Bash, Edit, Read, Write, MCP tools)
  - File-path patterns (e.g., `Edit(clients/sensitive-client/*)`)
  - Command patterns (e.g., `Bash(git push *)`)
  - Exit code `2` from the hook script = block the tool call
- **PostToolUse hooks** react after a tool runs (logging, notifications, follow-up actions)
- **Stop hooks** fire when the session ends (auto-commits, "did anything need to land in the wiki?" reminders)
- **SessionStart hooks** fire when a session opens (context loading, daily briefing)
- **UserPromptSubmit hooks** fire when a prompt is submitted (prompt enrichment, logging)

## What hooks CANNOT do (per Anthropic docs as of 2026-05)

Worth knowing so the access-control model is built on the right layer:

- **Hooks do not receive user identity.** There is no `$CLAUDE_USER` or equivalent env var.
- **Hooks do not integrate with GitHub permissions.** Repo access is enforced at the git/GitHub layer, not the Claude Code layer.
- **Hooks do not enforce role-based access control across users.** Each operator runs Claude Code locally with their own `~/.claude/settings.json`.

You *can* approximate per-role behavior with custom logic (a hook reading `whoami` and mapping it to a role file), but it's fragile, local-only, and easy to bypass. For a small team it isn't worth solving inside hooks.

**Control who-can-do-what at the GitHub layer instead:**
- **CODEOWNERS** — folder-level PR approvals (e.g., only leads merge to `wiki/`)
- **Branch protection** — `main` is protected; work happens in branches and merges via PR
- **Folder-based PreToolUse blocks** — same rule for everyone, but at least sensitive paths are harder to edit by accident

---

## What hooks do for Warm-Up-OS today

See `.claude/settings.json` for the live config. Currently:

### `PreToolUse` — block destructive operations
```json
{
  "matcher": "Bash(rm -rf *)",
  "hooks": [{ "type": "command", "command": "exit 2" }]
}
```

### `Stop` — end-of-session reminder
Prompts the operator to check whether anything from the session should land in `INDEX.md` or `wiki/`.

---

## Recommended hooks to add as the team grows

### `Stop` — auto-commit on session end
Each operator commits to a branch named after their user, then merges via PR. Keeps `main` clean and gives every change a review gate.

### `PostToolUse` — audit trail on `clients/`
After any Edit/Write to `clients/`, append a line to `.claude/logs/client-edits.log` so there's a record of who touched what.

### `SessionStart` — daily brief on session open
Read `INDEX.md`, show the active client list and any open follow-ups, so every operator starts oriented.

---

## Adding a hook

1. Add the matcher + command to `.claude/settings.json` under the right event.
2. Keep hook scripts fast and side-effect-light — they run on every matching event.
3. For blocking hooks, return exit code `2` to stop the tool call.
4. Test it on a throwaway action before relying on it.

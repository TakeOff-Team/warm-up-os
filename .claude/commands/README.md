# .claude/commands/

Slash commands. These string together multiple skills into single keystrokes for the team.

## Example commands (not yet built)

| Command | What it runs |
|---------|--------------|
| `/onboard-client <url>` | `client-context-from-url` → `octave-context-builder` → notification to #ops Slack |
| `/run-campaign <client>` | Loads client context, kicks off `cold-outreach-orchestrator` |
| `/weekly-sweep` | Runs `inbox-sweep` on `raw-context/`, generates the weekly summary |

## When to add a command

When a workflow involves chaining 2+ skills the same way every time. If the team is copy-pasting the same skill sequence, that's a command.

## Don't use commands for

- One-off skill calls — just invoke the skill directly
- Skills that need different parameters every time — keep those at the skill layer

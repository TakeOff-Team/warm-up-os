# .claude/agents/

Long-running multi-step workflows. Use when a single skill doesn't fit and you need parallelizable / persistent work.

## Skills vs. agents

**Use a skill when:**
- The task is one focused job
- The flow is linear or mostly linear
- It completes within a single Claude session

**Use an agent when:**
- The work has parallel steps that benefit from independent execution
- You need a subagent to maintain its own context separately
- The workflow spans multiple "thinking modes" (research → synthesis → execution)

## When to add an agent here

Most of the time, you don't need to. Build a skill first. If the skill grows past 200 lines and starts juggling unrelated concerns, consider splitting it into:
- A parent agent that orchestrates
- Child skills it calls

## Examples (placeholder — not built yet)

- `competitor-research-agent` — research a competitor across web + social, produces a structured report
- `client-onboarding-agent` — orchestrates client-context-from-url + octave-context-builder + Slack setup notification in parallel

# .claude/skills/

Reusable plays. One folder per skill. One skill, one task.

## What a skill looks like

```
.claude/skills/<skill-name>/
└── SKILL.md
```

A skill is a single markdown file with frontmatter that tells Claude:
- **`name`** — kebab-case identifier (matches folder name)
- **`description`** — what it does + trigger phrases. Claude reads this to decide when to use it.
- **`allowed-tools`** — which tools the skill can call (optional but recommended for production skills)

The body is the SOP. Tell Claude how to execute the task: required inputs, steps, failure modes.

## Rules

1. **One skill, one task.** If you're using "and" or "then" to describe what the skill does, it's two skills.
2. **Skills can call other skills.** Use orchestration patterns for multi-step flows. Keep the building blocks small.
3. **Skills should fail loud.** If a required input is missing, STOP. Don't guess.
4. **Skills should produce traceable artifacts.** Output files go somewhere predictable (`clients/<name>/campaigns/<name>/` etc.).

## Current skills

| Skill | Purpose |
|-------|---------|
| `client-context-from-url` | Scrape a client website + recent news → populate `clients/<name>.md` from template |
| `octave-context-builder` | Build full Octave library structure from client materials |
| `cold-outreach-orchestrator` | End-to-end cold outreach campaign |

## Adding a new skill

1. Create `.claude/skills/<skill-name>/SKILL.md`
2. Write frontmatter (name, description with trigger phrases, allowed-tools)
3. Write the body — inputs, steps, output, failure modes
4. Update `/INDEX.md` to register the new skill
5. Open a PR — skill changes require review (see CODEOWNERS in the real repo)

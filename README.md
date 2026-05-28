# Warm-Up-OS

The Warm Up company operating system — the shared brain every team member works out of when delivering for clients.

When you start a task, you open this repo in Claude Code. The **wiki** tells you how Warm Up does things. The **clients/** folder tells you what each client needs. The **.claude/skills/** folder gives you the reusable plays. You never start from a blank page.

This is **v0.5** — the bootstrap shape. It works today and grows as the team adds clients, skills, and playbooks.

---

## Quickstart

```bash
git clone https://github.com/TakeOff-Team/warm-up-os.git
cd warm-up-os
```

1. **Install prerequisites** — Claude Code + the MCPs the skills use. See [`SETUP.md`](SETUP.md).
2. **Open the folder in Claude Code.** It reads `CLAUDE.md` automatically and follows the session-start protocol.
3. **Run your first play.** To onboard a client:
   ```
   /client-context-from-url https://theirsite.com
   ```

If you don't know where something lives, open [`INDEX.md`](INDEX.md) — it's the content catalog.

---

## What's in here

```
warm-up-os/
├── CLAUDE.md          # Operating doc — Claude reads this every session
├── INDEX.md           # Content catalog — "where does X live?"
├── SETUP.md           # Install Claude Code + MCPs + .env
├── wiki/              # How Warm Up operates (services, ICP, frameworks, team roles, tooling)
├── clients/           # One file per client (offer, ICP, campaigns, Octave library)
├── raw-context/       # Inbox — drop transcripts/briefs here, sort later
├── archive/           # Old-but-valuable stuff (churned clients, deprecated skills)
└── .claude/
    ├── skills/        # Reusable plays — one skill, one task
    ├── agents/        # Longer multi-step workflows
    ├── commands/      # Slash commands that chain skills
    └── hooks/         # Lifecycle automations (guardrails, auto-commit)
```

---

## The skills (what works today)

| Skill | What it does | Run it |
|-------|--------------|--------|
| `client-context-from-url` | Scrape a client site + recent news → populate `clients/<name>.md` | `/client-context-from-url <url>` |
| `octave-context-builder` | Build the full Octave library (products, segments, personas) for a client | `/octave-context-builder` |
| `cold-outreach-orchestrator` | Context → list → copy → review → push a campaign in DRAFT | `/cold-outreach-orchestrator` |

Some skills depend on MCPs/keys you set up in [`SETUP.md`](SETUP.md). The cold-outreach orchestrator also expects a `defroster` copywriting skill — see SETUP for that dependency.

---

## How a typical task flows

**"Build a cold outreach campaign for Sustainment."**

1. Open the repo in Claude Code, run `/cold-outreach-orchestrator`.
2. It loads `clients/sustainment.md` for context, pulls the Octave library, builds a list, drafts copy.
3. It **stops for your review** — nothing sends without an explicit approval.
4. On approval it pushes the campaign to Instantly/HeyReach **in DRAFT**. You launch manually.
5. The run is logged back to the client file.

No new prompts. No "what did we do last time?" archaeology. The OS already knew.

---

## Operating principles (the short version)

- **One skill, one task.** If a skill needs "and"/"then" to describe it, split it.
- **Raw context first.** New material lands in `raw-context/`, gets sorted later.
- **Wiki is the institutional brain.** If you explain something twice, write it in `wiki/`.
- **Human review gates are non-negotiable.** Skills draft; people approve; nothing auto-sends.
- **Archive, don't delete.** History is too valuable to lose.

Full detail in [`CLAUDE.md`](CLAUDE.md).

---

## Access control

This is a single shared repo. Who-can-do-what is governed at the GitHub layer:

- **Branch protection** on `main` — work in branches, merge via PR
- **CODEOWNERS** — folder-level review (fill in `CODEOWNERS` with your GitHub handles)
- **Local guardrails** — `.claude/settings.json` blocks destructive ops for everyone

Never commit credentials. Secrets live in `.env` (gitignored); see `.env.example`.

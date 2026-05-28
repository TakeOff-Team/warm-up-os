# Warm-Up-OS

This is Warm Up's company operating system — the shared brain every team member works out of when delivering for clients.

**One-line summary:** When you start a new task, you load this repo. The wiki tells you how Warm Up does things. The `clients/` folder tells you what each client needs. The `.claude/skills/` folder gives you the reusable plays. You never start from a blank page.

---

## Session start protocol

**First run?** Check `.claude/setup-state.json`. If it's missing or any phase is still `pending`, the
OS isn't set up yet — run the **`warm-up-os-setup`** skill (`/warm-up-os-setup`) before anything else.
It's a guided, resumable wizard that connects tools, collects keys, and interviews the operator to fill
the wiki + client files. Once setup is complete, proceed normally.

Read in this order:

1. **This file** (`CLAUDE.md`) — operating principles + structure
2. **`INDEX.md`** — content catalog. If you don't know where something lives, look here first.
3. **`wiki/`** — Warm Up's institutional knowledge (services, ICP, frameworks, team roles, tooling)
4. **`clients/<client-name>.md`** — if you're working on a specific client, load their context

Then start work.

---

## Folder map

```
Warm-Up-OS/
├── CLAUDE.md              # You are here
├── INDEX.md               # Content catalog
├── PLANNING.md            # Plan-first checklist — read before any new build
├── wiki/                  # How Warm Up operates (the company brain)
├── clients/               # Per-client context (one .md per client)
├── raw-context/           # Inbox — unsorted material (transcripts, briefs)
├── archive/               # Old stuff that's too valuable to delete
└── .claude/
    ├── skills/            # One skill per task — flat, reusable
    ├── agents/            # Longer multi-step workflows
    ├── commands/          # Slash commands that string skills together
    └── hooks/             # Lifecycle automations (commits, alerts, etc.)
```

---

## Operating principles

### 1. One skill, one task

Every skill in `.claude/skills/` does ONE narrow thing. If a skill is doing two jobs, split it. Composability beats omnibus skills every time.

Examples:
- `client-context-from-url` — scrape a client site, output an Octave-shaped context file. That's it.
- `cold-outreach-orchestrator` — runs the full outreach campaign sequence. Calls other skills.

### 2. Raw context first

Anything new lands in `raw-context/` first. Transcripts, briefs, voice notes, scraped pages. Sort/clean later. Lower the barrier to capturing.

When `raw-context/` gets full (weekly), run `/inbox-sweep` to triage. Move what's useful into `wiki/` or `clients/<name>.md`. Archive the rest.

### 3. Wiki is the institutional brain

`wiki/` answers: "How does Warm Up actually do this?" — services, ICP, the frameworks the team uses, who does what. Everyone reads from `wiki/`. Only specific roles edit it (control via GitHub CODEOWNERS in the real repo).

If you find yourself explaining something twice, write it in `wiki/`.

### 4. Clients folder = one file per client (for now)

`clients/<client-name>.md` holds everything an operator needs to start work for a client: their offer, ICP, current campaigns, Slack channel, Airtable record, key team. Updated by whoever's working on the client most actively.

In a future iteration, this becomes `clients/<client-name>/` subfolders with campaigns/, copy/, lists/, and per-client context. The flat .md version is the v0.5 starting shape.

### 5. Archive, don't delete

If something's no longer current but might be useful to look back on (old playbooks, stale client context after they churn, deprecated skills) — move it to `archive/<topic>/`. Never delete history.

### 6. Permission and access control

For the v0.5 build, this is a **single shared repo** in GitHub. Access is governed by:

- **GitHub permissions:** who can clone, push, and merge
- **CODEOWNERS:** which roles can approve PRs to which folders (e.g., only senior operators can merge to `wiki/`)
- **Branch protection:** main branch is protected, work happens in branches
- **PreToolUse hooks (selective):** block destructive operations on critical paths (e.g., never delete `clients/`)

Per-user role-based access inside Claude Code itself is NOT how this layer works. Each operator runs Claude Code locally; the controls live at the GitHub layer.

---

## How a typical task flows

**Task: "Build a cold outreach campaign for Sustainment."**

1. Operator opens this repo, types in Claude Code: `/cold-outreach-orchestrator`
2. Skill loads `clients/sustainment.md` → has all the client context
3. Skill calls Octave MCP → pulls per-client library
4. Skill builds list → generates copy → human review gate → pushes campaign in DRAFT to Instantly
5. Operator launches manually
6. Result logged to `clients/sustainment.md` campaign log

The system answered "how do we run a cold campaign?" using:
- `wiki/frameworks.md` (Warm Up's cold outreach methodology)
- `clients/sustainment.md` (client-specific context)
- `.claude/skills/cold-outreach-orchestrator/` (the executable play)

No new prompts. No "what did we do last time?" archaeology. The OS already knew.

---

## When you don't know something

Before asking a human:

1. Search `INDEX.md`
2. Grep `wiki/` for keywords
3. Check if there's an existing skill in `.claude/skills/` that does this
4. Check `raw-context/` for relevant transcripts or briefs
5. Check `archive/` if it's a pattern from a prior client

If still stuck, ask in #warm-up-os Slack channel or tag the operator who owns the relevant `wiki/` section.

---

## Starting a new build

Before building anything new — a skill, a client automation, any infrastructure — read **`PLANNING.md`** and start in **plan mode** (`shift+tab`). Plan the build, check for an existing skill that already does it, get the plan approved, then execute. Don't skip to code.

---

## When you add something new

- New skill? Add to `.claude/skills/<skill-name>/SKILL.md`. Update `INDEX.md`.
- New client? Copy `clients/_TEMPLATE.md` → `clients/<client-name>.md`. Populate.
- New framework? Add to `wiki/frameworks.md`. Update `INDEX.md` if it's a major section.
- New transcript/brief? Drop in `raw-context/`. Don't sort yet.

---

## Versioning

This is **Warm-Up-OS v0.5** — the bootstrap shape. v1.0 ships when:
- Every active client has a populated `clients/<name>.md`
- All 5 wiki sections are complete (services, ICP, frameworks, team-roles, tooling)
- At least 10 skills are operational and used by the team
- One repeatable weekly task runs end-to-end without human intervention (other than the review gate)

---

## Don't do these things

- Don't write content directly in `CLAUDE.md` or `INDEX.md` that should live in `wiki/`
- Don't put per-client context anywhere except `clients/<name>.md`
- Don't commit credentials, API keys, or anything else that belongs in `.env`
- Don't merge to `main` without a PR review
- Don't create new top-level folders without team agreement — this structure is intentional

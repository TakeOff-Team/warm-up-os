---
name: warm-up-os-setup
description: Guided, resumable setup wizard for Warm-Up-OS. Walks a new operator through standing up the whole system — prerequisites, MCP installs, API keys, the wiki knowledge interview (services, ICP, frameworks, team), and client onboarding — one step at a time, and tracks progress so you can stop and pick up later. Triggers on "set up the OS", "set up warm-up-os", "onboard me", "finish setup", "continue setup", "/warm-up-os-setup", or any first run on a repo where setup isn't complete.
allowed-tools: Read, Write, Edit, Bash, Skill
---

# Warm-Up-OS — Setup Wizard

This skill stands up a fresh Warm-Up-OS by interviewing the operator and writing the answers into the
real files. It is a **sit-down**: it tracks progress in `.claude/setup-state.json` and can be stopped
and resumed at any time.

> **Instructions for Claude:** Execute this file conversationally. Work through the phases in order.
> Ask **one block at a time** and **wait for the answer** before moving on. After each phase finishes,
> update `.claude/setup-state.json`. Draft each file from the operator's answers and **show it for
> approval before writing**. Keep the tone like a sharp colleague helping a friend set up — direct,
> encouraging, no jargon dumps.

---

## Hard rules (do not break these)

1. **Never read, print, or write the contents of `.env`.** For API keys, you instruct and confirm
   only. The operator types keys into the file themselves. Do not `cat`, `Read`, or `grep` `.env`.
2. **One question block at a time.** Wait for the response. Don't batch the whole interview.
3. **Confirm before writing any file.** Show the draft, get a yes, then Write.
4. **Resumable.** Always read `.claude/setup-state.json` first. Never redo a `done` phase unless the
   operator asks.
5. **Don't fabricate.** If the operator doesn't know an answer yet, write `_TBD — revisit_` and move
   on. A half-filled wiki beats a wiki full of guesses.
6. **Update the state file after every phase**, not at the end. If the session dies mid-way, progress
   is saved.

---

## Phase 0 — Resume check (always run first)

1. Read `.claude/setup-state.json`.
2. If it doesn't exist, create it from this template and treat all phases as `pending`:
   ```json
   {
     "version": "0.5",
     "started_at": null,
     "completed_at": null,
     "phases": {
       "prereqs": "pending", "tooling": "pending", "env": "pending",
       "services": "pending", "icp": "pending", "frameworks": "pending",
       "team": "pending", "clients": "pending"
     },
     "notes": ""
   }
   ```
3. If progress already exists, print a short status table (phase → status) and ask:

   > "Looks like we've started this before. Want to **pick up where we left off**, **redo a specific
   > section**, or **start over**?"

   Route based on the answer. Default = resume at the first `pending` phase.
4. Set `started_at` to the current date if it's still null.

---

## Phase 1 — Orientation

Say something like:

> "I'm going to help you stand up your Warm-Up-OS. We'll go step by step: check your setup, get your
> tools connected, then I'll interview you about how Warm Up actually works so the system knows your
> services, your ICP, your playbooks, and your team. Whatever you tell me gets written into the repo —
> you don't touch the files, I do.
>
> This is a sit-down. We can stop any time and pick up later — I keep track of where we are. Figure 20
> to 40 minutes if we do it all in one go. Ready to start?"

Wait for "ready."

---

## Phase 2 — Prerequisites

Quick confirmation block. Ask:

> "First, the basics:
> - You're in Claude Code right now, inside the cloned `warm-up-os` folder — yes?
> - Is your git set up? (I can check.)"

- Run `git config user.name` and `git config user.email` to confirm identity is set. If missing, tell
  the operator the two commands to run (`git config --global user.name "…"` /
  `git config --global user.email "…"`).
- Confirm they cloned the repo (they're in it if this skill is running).

Mark `prereqs: "done"`.

---

## Phase 3 — Tooling & MCPs

Explain: the skills call outside tools through MCP servers. We'll go one at a time and only connect
what Warm Up actually uses.

Run `claude mcp list` once to see what's already installed. Then for each tool below, ask if Warm Up
uses it; if yes and it's not installed, walk them through it.

Tools (in order): **Octave, Firecrawl, Perplexity, Clay, Instantly, HeyReach** (and optionally
Apollo / Prospeo).

For each one Mike uses but hasn't installed, give the install pattern from `SETUP.md`:

> "Install pattern — use BOTH flags or it'll break across folders:
> ```
> claude mcp add <name> --transport http -s user <server-url>
> ```
> (Some vendors ship an npx/stdio server instead — use their published command, but keep `-s user`.)"

After each install, have them run `claude mcp list` to confirm it shows up.

When the tool list is settled, update the **Access** column of the table in `wiki/tooling.md` to
reflect what's actually connected vs. not (e.g., "MCP installed ✅" / "not connected yet"). Show the
edit, confirm, write.

Mark `tooling: "done"`.

---

## Phase 4 — API keys (.env)

**Hands-off — you never touch the keys.** Say:

> "Now the keys. I'm not going to handle these — you'll paste them straight into the `.env` file so they
> never go through this chat. Open `.env` in your editor (copy it from `.env.example` first if you
> haven't: `cp .env.example .env`). Based on the tools you're using, fill in these:"

List only the keys matching the tools they said yes to in Phase 3 (names are in `.env.example`:
`FIRECRAWL_API_KEY`, `PERPLEXITY_API_KEY`, `OCTAVE_API_KEY`, `CLAY_API_KEY`, `INSTANTLY_API_KEY`,
`HEYREACH_API_KEY`, optionally `APOLLO_API_KEY` / `PROSPEO_API_KEY`).

Remind them: `.env` is gitignored, so it never gets committed. When they say they've saved it, take
their word — **do not read the file to verify**.

Mark `env: "done"`.

---

## Phase 5 — Services interview → `wiki/services.md`

This is the first knowledge block. Ask one sub-block at a time:

**5a — Core offerings.**
> "What does Warm Up actually sell? List your core services. For each, a sentence on what it is.
> Example: 'Cold outreach — multi-channel LinkedIn + email campaigns, signal-driven targeting.'"

**5b — What you don't do.**
> "What do clients ask for that you deliberately *don't* offer? (e.g., paid media, brand work, SEO.)"

**5c — Pricing model.**
> "How do you price? Retainer ranges, performance components, setup fees — whatever's real. Rough is
> fine."

**5d — Deliverables per service.**
> "For each core service, what does a client actually get, and on what cadence?"

Draft `wiki/services.md` (keep the existing section headers), show it, confirm, write.
Mark `services: "done"`.

---

## Phase 6 — ICP interview → `wiki/icp.md`

**6a — Primary ICP.**
> "Who's your best-fit client? Company size, industry, the role you sell to, and the trigger that makes
> them a fit *right now*."

**6b — Secondary ICPs.**
> "Any secondary segments you'll happily take?"

**6c — Anti-ICP.**
> "Who do you turn away? The clients that are more trouble than they're worth."

**6d — Why this ICP.**
> "Why do you win with the primary segment specifically — margin, retention, referrals, case-study
> velocity?"

Draft `wiki/icp.md`, confirm, write. Mark `icp: "done"`.

---

## Phase 7 — Frameworks interview → `wiki/frameworks.md`

Walk these one at a time (skip any that don't apply — mark `_TBD_`):

**7a — Cold outreach methodology.** List building (Clay vs. Prospeo/Apollo — when each), copy
structure + sequence length/cadence, what gets personalized vs. templated, review gates.

**7b — Octave context structure.** The standard shape of a per-client Octave library (products,
segments, personas, positioning notes).

**7c — Signal-driven targeting.** The Trigify → Clay → outreach pattern, if used.

**7d — DNC / suppression handling.** Known gaps + workarounds (e.g., HeyReach DNC).

**7e — Client onboarding sequence.** Signed contract → first campaign live, day by day.

**7f — When to escalate to Mike.** What an operator handles vs. flags up.

Draft `wiki/frameworks.md`, confirm, write. Mark `frameworks: "done"`.

---

## Phase 8 — Team interview → `wiki/team-roles.md`

For each person on the team, capture: **role / what they do**, **tools they use**, **bottleneck**,
**AI insertion point** (where a skill removes friction), **what stays human**.

Ask role by role:
> "Who's on the team? Let's go one person at a time. First person — what's their role and what do they
> actually do day to day?"

Then for each, walk the five fields. Keep Mike's own founder row (already in the file) and refine it
with his input.

Draft `wiki/team-roles.md`, confirm, write. Mark `team: "done"`.

---

## Phase 9 — Clients

For each active client (the repo ships `clients/sustainment.md` + `clients/wabash-plastics.md`; ask if
there are others):

1. **Auto-populate if possible.** If Firecrawl is connected (Phase 3), call the
   `client-context-from-url` skill with the client's website to scrape + populate the file. If
   Firecrawl isn't set up, interview Mike for the basics instead (company snapshot, their ICP, their
   offer).
2. **Fill the manual bits** the scrape can't get: Slack channel, Airtable record, operator/owner,
   champion + decision maker.
3. Confirm each client file, then move to the next.

Ask if Mike wants to add any brand-new clients now (copy `clients/_TEMPLATE.md` →
`clients/<slug>.md`). Mark `clients: "done"` when the active list is covered.

---

## Phase 10 — Wrap-up

1. Set `completed_at` in the state file and confirm every phase is `done`/`skipped`.
2. Give a short recap: what's now populated (`wiki/*`, which clients) and what's still `_TBD_`.
3. Flag remaining dependencies if relevant — e.g., `cold-outreach-orchestrator` needs a `defroster`
   copywriting skill that isn't in the repo yet (see `SETUP.md`).
4. Point at the first real plays:
   > "You're set up. Try `/cold-outreach-orchestrator` to build your first campaign, or
   > `/client-context-from-url <url>` to onboard the next client."
5. Suggest committing the work:
   > "Want me to commit everything we just filled in? (I'll keep `.env` out — it's gitignored.)"

---

## Updating the state file

After each phase, Edit `.claude/setup-state.json` and set that phase to `"done"` (or `"skipped"`).
Keep `notes` short — anything a future sitting should know (e.g., "HeyReach not connected yet, revisit
keys"). Never put secrets in `notes`.

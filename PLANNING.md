# Planning Phase

Read this before starting any new build inside Warm-Up-OS — a new skill, a client automation, or any piece of infrastructure. **Plan first, build second.** Work through it in order; don't write code until the plan is approved.

> **Why plan first:** when AI does every step directly, errors compound — five steps at 90% each is only 59% overall. Plan mode front-loads the thinking and is the closest thing to one-shotting a build. The extra tokens up front save you the rework later.

---

## 1. Setup

- Open the `warm-up-os` repo in your IDE.
- Switch Claude Code to **Plan Mode** — `shift+tab` cycles through default → accept-edits → plan → auto. **Start every new build in plan mode.**
- Use a strong model for the planning itself. The thinking is worth it.

## 2. Brief — brain-dump the goal

In plain language (type it, or use Whisper): what are you building, and why? Who uses it? What does "done" look like? One paragraph is enough. Don't design yet — just capture intent.

## 3. Check what already exists (before building anything)

- Scan `.claude/skills/` — is there already a skill for this? **One skill, one task — don't duplicate.**
- Search `INDEX.md`, grep `wiki/`, check `archive/` for prior patterns.
- Reuse beats rebuild. Only make something new when nothing fits.

## 4. Define the approach

- Is this one skill, or a skill that calls others? Composability beats omnibus.
- What context does it need? Name the `wiki/` and `clients/` files it will read.
- What tools? Prefer **APIs over MCPs** for execution (see `wiki/tooling.md`); MCPs for conversational work.
- Where do outputs land? Predictable paths. Never secrets in the repo — those live in `.env`.

## 5. Lock the guardrails

- **Read-only by default.** A human review gate before anything sends or writes. No autonomous sends.
- Name what could go wrong, and where the hard stop is.

## 6. Get the plan approved before building

- Review the plan — yourself, or a teammate via PR — before writing any code or skills.
- Only move into execution once it's approved.

---

## After it's built — close the loop

When something breaks or you find a better way: fix it, verify the fix, then update the relevant skill or `wiki/` page so the system gets stronger next time. Don't overwrite standing instructions (skills, wiki) without asking unless told to — refine them, don't discard them.

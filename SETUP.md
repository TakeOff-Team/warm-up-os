# Setup

Get Warm-Up-OS running locally. ~15 minutes.

> **Prefer the guided version?** Open the repo in Claude Code and run **`/warm-up-os-setup`** — it
> walks you through everything below conversationally, interviews you to fill the wiki + client files,
> and tracks your progress so you can stop and resume. This doc is the manual reference the wizard
> leans on.

---

## 1. Install Claude Code

If you don't have it yet:

```bash
npm install -g @anthropic-ai/claude-code
```

Then open this folder:

```bash
cd warm-up-os
claude
```

Claude reads `CLAUDE.md` on session start and follows the operating protocol automatically.

---

## 2. Environment variables

Copy the example file and fill in your keys:

```bash
cp .env.example .env
```

`.env` is gitignored — it never gets committed. Never paste keys into any other file.

---

## 3. MCP servers

The skills call external tools through MCP servers. Install each one **at user scope** so it works across every folder, not just this one.

> **Install pattern (important):** use BOTH `--transport http` AND `-s user`. Missing either flag is the most common reason an install "works" but then errors out or only works in one directory.
>
> ```bash
> claude mcp add <name> --transport http -s user <server-url>
> ```
>
> Some vendors ship a stdio/npx server instead of an HTTP endpoint — follow that vendor's published install command, but keep `-s user` so it's available everywhere.

| MCP | Used by | Needed for |
|-----|---------|-----------|
| **Firecrawl** | `client-context-from-url` | Scraping client websites |
| **Perplexity** | `client-context-from-url`, `octave-context-builder` | Recent-news enrichment |
| **Octave** | `octave-context-builder`, `cold-outreach-orchestrator` | Per-client context library |
| **Clay** | `cold-outreach-orchestrator` | List building (signals + enrichment) |
| **Instantly** | `cold-outreach-orchestrator` | Email campaign push (DRAFT) |
| **HeyReach** | `cold-outreach-orchestrator` | LinkedIn campaign push (DRAFT) |

After installing, verify:

```bash
claude mcp list
```

---

## 4. Skill readiness — what runs today vs. what needs setup

| Skill | Runs now if you have… | Notes |
|-------|----------------------|-------|
| `client-context-from-url` | Firecrawl + Perplexity MCP | Fully functional once those two are installed |
| `octave-context-builder` | Octave MCP (+ Perplexity) | Writes a local snapshot even if the Octave push fails |
| `cold-outreach-orchestrator` | Octave + Clay + Instantly/HeyReach MCP **and a `defroster` skill** | See the dependency below — the copy step hard-stops without it |

### The `defroster` dependency

`cold-outreach-orchestrator` does **not** write copy itself. It calls a separate `defroster` copywriting skill for that, by design (one skill, one task). That skill is **not included in this repo yet.**

Until you add `.claude/skills/defroster/SKILL.md`, the orchestrator will run context → list, then **stop at the copy step** and tell you Defroster is missing. That's intentional — it fails loud rather than silently writing off-brand copy. Two options:

1. **Add a `defroster` skill** — a focused copywriting skill that takes the Octave library + ICP signal + a sample contact and returns a sequence. Drop it at `.claude/skills/defroster/SKILL.md` and register it in `INDEX.md`.
2. **Write copy manually** for a given run, then feed it back to the orchestrator at the review gate.

---

## 5. Guardrails (already on)

`.claude/settings.json` ships with safety rails:

- Blocks `rm -rf` and `git reset --hard`
- Blocks edits to `archive/` (history is read-only)
- Asks before `git push`, `git merge`, and edits to `wiki/` or `.env`
- Stop-hook reminder to land learnings in `INDEX.md` / `wiki/`

Adjust in `.claude/settings.json` as the team's needs change. See `.claude/hooks/README.md` for what hooks can and can't do.

---

## 6. First run

```
/client-context-from-url https://sustainment.us
```

This scrapes the site, enriches with recent news, and populates `clients/sustainment.md`. Review the output, and you're operating.

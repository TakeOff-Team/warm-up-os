# Warm Up — Tooling Stack

The tools Warm Up uses. Source of truth for: which tool does what, API or MCP, known limitations.

---

## Active stack

| Tool | Purpose | Access | Notes |
|------|---------|--------|-------|
| **Octave** | Per-client context library (ICPs, personas, case studies, products) | MCP (installed) + API docs | Foundation of every client workflow |
| **HeyReach** | LinkedIn outreach campaigns | API primary | DNC limitation: adding to DNC does NOT remove from active campaigns. Manual fix. |
| **Instantly** | Email outreach | API primary | Clean REST API. Requires ≥1 seed lead before populating template vars. |
| **Clay** | Enrichment + workflow tables | MCP primary | Recipe + signal-driven targeting |
| **Trigify** | Social listening (scrape post-likers) | TBD | Use case: Trigify → Clay enrichment → outreach |
| **Apollo / Prospeo** | Raw contact filtering (when Clay overkill) | API | Use when ICP is clean title + industry + size |
| **Obsidian** | Knowledge vault (Mike's personal second brain) | CLI + QMD MCP | |
| **Claude Code** | Primary AI harness | — | Where work happens |
| **Cowork** | Team-friendly Claude interface | — | For team members not comfortable in Claude Code directly |
| **Slack** | Team comms | Slack MCP if needed | |
| **Airtable** | Client / campaign tracking | API | One record per client linked from `clients/<name>.md` |

---

## Design principles

### APIs > MCPs (when possible)

Token savings 25-98% depending on use case. MCPs load heavy context. APIs + scripts stay lean.

**Use MCP when:**
- The interaction is conversational
- The tool's model (tables, workflows) doesn't fit raw API calls cleanly (Clay)

**Use API when:**
- The skill is executing a structured task (build list, push campaign)
- Token efficiency matters at scale

**Hybrid default:** Install MCP for convo. Store API docs in `wiki/api-docs/` (to be created) for skill use.

---

## Known integration gaps

| Gap | Workaround | Skill candidate? |
|-----|-----------|------------------|
| HeyReach: DNC doesn't remove from active campaigns | API call to active-campaign endpoint to remove | Yes — `heyreach-dnc-sweep` |
| Instantly positive reply ≠ HeyReach state | Manual cross-tool sync | Yes — `cross-tool-state-sync` |
| Trigify → Clay handoff | Plug-in path exists | Yes — `trigify-to-clay-pipeline` |

---

## Tool decision flow

When building a new skill, ask:

1. Is this conversational or executable? (conversational → MCP; executable → API)
2. Does the tool have a clean API? (yes → API; no → MCP if MCP exists)
3. Is this part of a larger chain? (yes → API for token efficiency; chain skills via orchestrator)

---

## API docs storage (future)

`wiki/api-docs/<tool-name>/` will hold copy-pasted (or MCP-pulled) API documentation per tool, so skills can reference docs locally without re-fetching every time. Not built yet.

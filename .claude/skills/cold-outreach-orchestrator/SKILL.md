---
name: cold-outreach-orchestrator
description: End-to-end cold outreach campaign builder. Pulls client context from Octave, builds a contact list (Clay or Prospeo/Apollo), generates copy by calling the Defroster sub-skill, gates on human review, then pushes the campaign live to Instantly (email) or HeyReach (LinkedIn). Triggers on "build a cold campaign", "run outreach for [client]", "new campaign for [client]", "/cold-outreach-orchestrator".
allowed-tools: Read, Write, Bash, mcp__octave__*, mcp__clay__*, mcp__instantly__*, mcp__heyreach__*
---

## What this skill does

Orchestrates a full cold outreach campaign — context → list → copy → review → push — across Mike's stack. Does NOT do the copywriting itself; it calls the existing **Defroster** copywriting skill for that. This is the conductor, Defroster is the soloist.

---

## STEP 0 — Required inputs gate (RUN BEFORE ANYTHING ELSE)

Before doing anything — before pulling Octave, before touching Clay, before any tool call — confirm these three inputs are present. If ANY are missing or ambiguous, STOP and ask. Do not guess. Do not proceed with placeholders.

### The three required inputs

1. **`client`** — which client is this campaign for?
   - Required: name that maps to an Octave library (e.g., `sustainment`, `acme-mfg`)
   - Why required: no client = no context layer = no skill

2. **`channel`** — `email` or `linkedin`
   - Required: determines whether final push goes to Instantly or HeyReach
   - Why required: the entire execution layer depends on this

3. **`icp_signal`** — who are we targeting and what's the trigger?
   - Required: titles + companies + signal (e.g., "VPs of Supply Chain at US manufacturers, 50–500 employees, who recently raised funding OR made a senior hire in the last 90 days")
   - Why required: Octave context tells us *what* the client sells; ICP signal tells us *who to send it to*. Both are needed.

### How to handle missing inputs

If any of the three are missing, ask in this exact order — one question at a time, no batching:

```
Before I kick this off, I need three things:

1. Which client is this campaign for? (must match an Octave library)
2. Channel — email or linkedin?
3. Who's the target ICP + what's the signal? (titles, company filters, trigger)
```

Wait for all three before proceeding. **Never** proceed with two of three.

### Optional inputs (sensible defaults — only ask if Mike volunteers them)

| Input | Default | Override when |
|-------|---------|---------------|
| `list_size` | 1000 | <500 = micro-campaign, >1500 = needs sub-segmentation |
| `list_source` | Clay (if recipe-able) → fallback Prospeo/Apollo | Mike specifies |
| `sequence_length` | 3 emails | Mike specifies |
| `cadence_days` | 7–12 day spread | Mike specifies |
| `campaign_name` | `{client}-{channel}-{YYYYMMDD}` | Mike specifies |
| `order` | list-first, then copy | Mike says `--copy-first` |

---

## STEP 1 — Pull context layer (Octave MCP)

Once gate passes, hit Octave MCP for the client's library.

**Pull:**
- Products / services
- Segments
- Personas
- Any positioning notes Mike has hand-edited

**Output to user:** brief one-paragraph summary of what Octave returned. Confirm the right library loaded before moving on. If Octave returns nothing or a stale library, STOP and ask Mike whether to proceed with what's there or refresh it first.

---

## STEP 2 — Build contact list (default: list-first)

Based on `icp_signal`, build the contact list. Default order is list-first; if `--copy-first` flag was passed, skip to Step 3 and come back here.

### Source decision logic

- **Use Clay MCP if:** the ICP needs enrichment, signals (funding, hiring, tech-stack), or recipe-style waterfalls.
- **Use Prospeo / Apollo API if:** the ICP is a clean filter (title + industry + size) and Mike just wants raw contacts fast.

If unsure → ask Mike: "Clay (recipe + signals) or Prospeo/Apollo (raw filters)?"

### Size sanity check

After list builds:

- `< 500 contacts` → flag as micro-campaign. Confirm with Mike before continuing.
- `500–1500` → green. Proceed.
- `> 1500` → flag for sub-segmentation. Ask if Mike wants to split into sub-campaigns by segment.

### Output

- Save list to client vault: `clients/{client}/campaigns/{campaign_name}/list.csv`
- Show Mike the count + a 3-row preview before moving on.

---

## STEP 3 — Generate copy (calls Defroster sub-skill)

Hand off to the `defroster` skill. Pass:

- Octave library (from Step 1)
- ICP signal
- Target sequence length (default 3)
- Cadence (default 7–12 day spread)
- A 1–3 row sample from the contact list (so Defroster can populate variables and show realistic examples)

**Important:** if Defroster is not available in this vault, STOP and tell Mike: "Defroster not found — point me at it or build copy manually for this run." Do not silently fall back to writing copy in this skill.

### Output

- Save sequence to: `clients/{client}/campaigns/{campaign_name}/copy/`
  - `email-1.md`, `email-2.md`, `email-3.md` (or `dm-1.md` etc. for LinkedIn)
- Show Mike the full sequence inline.

---

## STEP 4 — HUMAN REVIEW GATE (HARD STOP)

This is the non-negotiable trust-and-verify moment. Do NOT proceed to execution without explicit approval.

### What to show Mike

```
─────────────────────────────────────
  CAMPAIGN READY FOR REVIEW
─────────────────────────────────────
  Client:       {client}
  Channel:      {channel}
  List size:    {n} contacts
  Sequence:     {n} messages over {n} days
  Files:        clients/{client}/campaigns/{campaign_name}/
─────────────────────────────────────
```

Then wait. Mike has three options:

| Mike says | Action |
|-----------|--------|
| `approve` / `ship it` / `looks good` | Proceed to Step 5 |
| `regenerate copy` | Re-run Step 3 with any edit notes Mike provides |
| `regenerate list` | Re-run Step 2 with any edit notes Mike provides |
| anything else | Treat as feedback. Iterate on whichever artifact he commented on. |

**Never auto-approve.** "Sounds good" in passing is not approval. Require an explicit ship signal.

---

## STEP 5 — Push to execution layer

Branch on `channel`.

### 5a — Email path (Instantly MCP)

1. **Seed ≥1 lead first.** Instantly needs at least one lead in the campaign before it'll let you populate template variables. Pull the first row from the contact list and add it as a seed.
2. Build the campaign in Instantly:
   - Name: `campaign_name`
   - Sequence: load each email from `copy/`
   - Variables: map from list columns
   - Cadence: as specified
3. Add the rest of the list.
4. Confirm campaign is in DRAFT state — do NOT auto-launch. Mike launches manually from Instantly UI.

### 5b — LinkedIn path (HeyReach MCP)

1. Build the campaign in HeyReach:
   - Name: `campaign_name`
   - Sequence: load each message from `copy/`
   - Variables: map from list columns
   - Cadence: as specified
2. Load the full contact list.
3. Confirm campaign is in DRAFT state — do NOT auto-launch.

> Known gap: HeyReach has a DNC limitation (Mike flagged in kickoff). Out of scope for v1.

---

## STEP 6 — Confirm + log

Final output back to Mike:

```
✅ Campaign built — {campaign_name}

Channel:    {channel} ({Instantly | HeyReach})
List:       {n} contacts
Sequence:   {n} messages
Status:     DRAFT (launch manually)

URL:        {execution_url}
Vault:      clients/{client}/campaigns/{campaign_name}/
```

Append a one-line entry to `clients/{client}/campaigns/log.md`:

```
- {YYYY-MM-DD} {campaign_name} — {channel}, {n} contacts, status: draft
```

---

## What this skill does NOT do

- Send emails or LinkedIn messages directly. Mike launches from the UI.
- Dedupe against HubSpot (out of scope v1).
- DNC-check HeyReach (out of scope v1).
- Re-build context Octave already has. Always pull from Octave; never re-derive from the website inside this skill.
- Replace Defroster. If Defroster is missing, this skill stops.

---

## Failure modes — handle, don't swallow

| If… | Then… |
|------|-------|
| Octave returns empty / wrong library | STOP. Ask Mike to verify client name. |
| List < 500 | FLAG as micro-campaign. Confirm before proceeding. |
| List > 1500 | FLAG. Offer to sub-segment. |
| Defroster not found | STOP. Tell Mike, do not silently substitute. |
| Instantly seed step fails | STOP. Show error. Do not skip to next step. |
| Mike says nothing for >1 turn at the review gate | Wait. Do not assume. |

---

## Connects to

- `defroster` — copywriting sub-skill, called in Step 3
- Octave MCP — context layer (Step 1)
- Clay MCP / Prospeo / Apollo — list building (Step 2)
- Instantly MCP — email execution (Step 5a)
- HeyReach MCP — LinkedIn execution (Step 5b)

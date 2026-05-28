---
name: octave-context-builder
description: Builds a full Octave-shaped context library for a client — products/services, segments, personas, case studies, positioning notes. Run AFTER client-context-from-url to deepen the Octave library before campaigns. Triggers on "build Octave context for [client]", "deepen [client] context", "/octave-context-builder".
allowed-tools: Read, Write, Edit, mcp__octave__*, mcp__perplexity__perplexity_search, mcp__perplexity__perplexity_research
---

## What this skill does

Takes a client (already onboarded via `client-context-from-url`) and builds out the full Octave library structure for them: products/services, segments, personas, positioning notes. Output lives in Octave (via MCP) AND a mirrored copy in `clients/<client_slug>/octave-snapshot.md` for offline reference.

**This is the deeper context build.** Run this after `client-context-from-url` has populated the basic `clients/<client_slug>.md`. The basic file gets you 80% of what you need for one-off tasks. The Octave library gets you the cross-campaign reusable context layer.

---

## STEP 0 — Required inputs gate

1. **`client_slug`** — must match an existing `clients/<client_slug>.md` file
2. **`octave_library_slug`** (optional) — if Mike already has one started, link to it; otherwise create new

If `client_slug` doesn't have a `clients/<client_slug>.md` file, STOP and tell Mike to run `client-context-from-url` first.

---

## STEP 1 — Load existing client context

Read `clients/<client_slug>.md`. Extract:
- Company snapshot
- Their ICP (titles, industries, signals)
- Their offer (services, differentiators, proof points)
- Key team
- Recent context

Use this as the base layer for everything below.

---

## STEP 2 — Products / services

Build the products array. For each product/service:

- **name**: short name
- **description**: 2-3 sentences from client's own words
- **target customer**: who this specific product serves (may differ from overall ICP)
- **value prop**: the outcome the buyer gets
- **proof points**: case studies, metrics, marquee logos for this specific product

If the client sells one thing → one product entry. If they sell three things → three entries. Don't merge unrelated offers.

---

## STEP 3 — Segments

For each distinct customer segment the client serves, build a segment entry:

- **segment name**: e.g., "Mid-market commercial real estate developers in Texas"
- **size cut**: revenue band, employee count, geography
- **industry/vertical**
- **buying trigger**: what makes this segment a fit RIGHT NOW (funding, hiring, product launch, regulatory change)
- **relevant products**: which of the client's products fit this segment

Most clients have 1-3 distinct segments. If you're at 5+, you're slicing too thin.

---

## STEP 4 — Personas

For each segment, build personas. A persona is a specific buying role:

- **persona name**: e.g., "VP of Facilities"
- **segment they live in**: link to segment above
- **what they care about**: 3-5 priorities for this role
- **what they're measured on**: how they're evaluated by their employer
- **objections they raise**: common pushback during sales conversations
- **proof they need**: what evidence they require to say yes

Each segment should have 1-3 personas (typically: champion + decision maker + influencer).

---

## STEP 5 — Positioning notes (Mike-overrides)

Octave's defaults are good, but Mike usually has overrides per client. Surface a section for Mike to fill:

```markdown
## Positioning overrides (Mike's edits to Octave defaults)

_Anything Octave generates that needs to be overridden for this client:_
- Voice changes:
- Words to avoid:
- Words to prefer:
- Taboo topics:
- Required disclaimers:
```

Mike fills this manually. The skill doesn't generate it.

---

## STEP 6 — Write to Octave (via MCP) + mirror to client folder

### 6a — Push to Octave MCP

Use `mcp__octave__*` to create or update the client's Octave library with the products, segments, personas, positioning notes from Steps 2-5.

If a library with `octave_library_slug` already exists:
- Diff existing vs. new
- Show Mike what's changing
- Wait for `approve` before pushing updates

If creating new:
- Create with `client_slug` as the library slug (unless `octave_library_slug` provided)
- Confirm creation succeeded

### 6b — Mirror snapshot locally

Write `clients/<client_slug>/octave-snapshot.md` with the full structured output (products, segments, personas, positioning notes). This is the offline reference — readable in the repo without needing Octave MCP loaded.

**File path:** if `clients/<client_slug>/` folder doesn't exist, create it. This is where per-client structured artifacts will live going forward (v0.5 → v1.0 transition).

### 6c — Update frontmatter

Edit `clients/<client_slug>.md` frontmatter:
- Set `octave_library`: `<octave_library_slug>`
- Set `octave_last_refreshed`: today (YYYY-MM-DD)

---

## STEP 7 — Output + review gate

```
─────────────────────────────────────
  OCTAVE LIBRARY BUILT — <client_slug>
─────────────────────────────────────
  Library slug:    <octave_library_slug>
  Products:        <n>
  Segments:        <n>
  Personas:        <n>
  Positioning:     [populated | TBD for Mike]
  Octave snapshot: clients/<client_slug>/octave-snapshot.md
─────────────────────────────────────
```

Wait for Mike. Options:
- `looks good` — done
- `re-do segments` / `re-do personas` etc. — re-run that specific step
- `add a segment for X` — add and re-push to Octave

---

## What this skill does NOT do

- Build campaigns — that's `cold-outreach-orchestrator`
- Generate copy — that's `defroster`
- Replace Mike's judgment on positioning — positioning overrides stay manual
- Fully replace human-built Octave libraries — this is the bootstrap, Mike refines from there

---

## Failure modes

| If… | Then… |
|------|-------|
| `clients/<client_slug>.md` doesn't exist | STOP. Tell Mike to run `client-context-from-url` first. |
| Octave MCP unavailable | Mirror snapshot still gets written. Tell Mike the Octave push failed, retry when MCP is back. |
| Existing Octave library has user edits | STOP. Show diff. Don't overwrite Mike's edits without explicit `approve overwrite`. |
| Client has no clear ICP | Generate best-effort segments, flag for Mike's review. Don't fabricate confidence. |

---

## Connects to

- `client-context-from-url` — prereq, must run first
- `cold-outreach-orchestrator` — uses the Octave library this skill builds
- `defroster` — uses Octave personas for copy
- Octave MCP — primary write target
- Perplexity MCP — for segment trigger validation (e.g., "is X really a current signal for this ICP?")

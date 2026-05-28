---
name: client-context-from-url
description: Scrape a client website + enrich with recent news, then populate clients/<client-name>.md from the Warm-Up-OS template. Triggers on "build context for [client]", "onboard [client]", "scrape [url] as a client", "/client-context-from-url".
allowed-tools: Read, Write, Edit, Bash, mcp__firecrawl__firecrawl_scrape, mcp__firecrawl__firecrawl_search, mcp__perplexity__perplexity_search, mcp__perplexity__perplexity_research, mcp__apify__call-actor, mcp__apify__search-actors
---

## What this skill does

Takes a client URL → scrapes the site → enriches with recent news → outputs a populated `clients/<client-slug>.md` matching the Warm-Up-OS template. This is the entry point for onboarding any new client into the OS.

**Output:** A populated `clients/<client-slug>.md` file with company snapshot, their ICP, offer, key team (if visible), and recent news section.

**What it does NOT do:** Build the full Octave library (that's `octave-context-builder`). Link Slack/Airtable (manual). Add per-client custom prompts (Mike does that after reviewing output).

---

## STEP 0 — Required inputs gate

Before doing anything, confirm:

1. **`url`** — the client's primary website URL (must be reachable)
2. **`client_slug`** — kebab-case identifier (optional; derive from URL domain if not provided, e.g., `sustainment.us` → `sustainment`)

If `url` is missing, STOP and ask:

```
Give me the client's website URL and (optionally) what to call them in the file (kebab-case).
```

Wait for the URL before proceeding.

---

## STEP 1 — Check for existing file

Look for `clients/<client_slug>.md`. If it exists:

- Tell the user: "Found existing file at `clients/<client_slug>.md`. Refresh the 'Recent context / news' section only? Or do a full re-scrape (overwrites company snapshot, ICP, offer)?"
- Wait for `refresh` or `full`.

If file does NOT exist, copy `clients/_TEMPLATE.md` → `clients/<client_slug>.md` and proceed.

---

## STEP 2 — Scrape the client website

Use **Firecrawl MCP** (`mcp__firecrawl__firecrawl_scrape`) to pull the main site:

- Target URL: the `url` input
- Format: markdown
- Include: title, headings, body text, meta description
- Wait for: page to fully render (JS-heavy sites)

Then run a follow-up scrape on common pages:

- `<url>/about`
- `<url>/team` or `<url>/about/team` or `<url>/people`
- `<url>/services` or `<url>/products`
- `<url>/case-studies` or `<url>/customers`
- `<url>/pricing`

If any 404, skip silently. Collect what's there.

**Output:** in-memory structured data with company description, services/products, team members (if listed), case studies (if listed).

---

## STEP 3 — Enrich with recent news

Use **Perplexity MCP** (`mcp__perplexity__perplexity_search`) with these queries:

- `"<company name>" funding 2025 OR 2026`
- `"<company name>" hires OR appointment 2025 OR 2026`
- `"<company name>" product launch OR announcement 2025 OR 2026`
- `"<company name>" news`

Use `search_recency_filter: "month"` for the news query to filter out stale results.

Extract: any funding rounds (amount, lead investor, date), notable hires (name, role, prior company), product launches, leadership changes, recent press.

**If Perplexity returns nothing recent:** that's fine, just leave "Recent context" section with a `_No recent news found as of YYYY-MM-DD_` placeholder.

---

## STEP 4 — Populate the template

Open `clients/<client_slug>.md` and fill in:

### Frontmatter
- `client_name`: company's actual brand name (from site title or H1)
- `status`: `prospecting` if this is a discovery scrape; `active` if Mike confirms it's a real client
- `onboarded`: today's date (YYYY-MM-DD)
- `operator`: leave as `[TBD]` for Mike to assign
- `website`: the `url` input
- Leave `slack_channel`, `airtable_record`, `octave_library` as placeholders

### Company snapshot
- One sentence pulled from the homepage hero text or "about us" page

### Their ICP
- Infer from case studies + customer logos + their own pitch language
- If you can't infer cleanly, write `_TBD — Mike to confirm in review_`
- Don't fabricate

### Their offer
- Core service from services/products page
- Differentiator from messaging copy
- Proof points from case studies / logos

### Key team
- Populate the table if a team page exists
- Otherwise leave empty with `_No team page found_`

### Recent context / news
- 3-5 bullets max
- Each bullet: date + headline + source link (in parens)
- Most recent first

---

## STEP 5 — Output + review gate

Show Mike the populated file content. Then:

```
─────────────────────────────────────
  CLIENT CONTEXT BUILT — <client_slug>.md
─────────────────────────────────────
  File:        clients/<client_slug>.md
  Sections:    Snapshot, ICP, Offer, Team, Recent news
  Inferred:    [list any sections we inferred vs. extracted]
  Missing:     [list any sections marked TBD]
─────────────────────────────────────
```

Wait for Mike. Options:

| Mike says | Action |
|-----------|--------|
| `looks good` / `ship it` | Done. Skill exits. |
| `re-scrape` | Go back to Step 2 with same URL |
| `wrong ICP` / `wrong offer` etc. | Take notes, re-run that specific section using user-provided correction |
| `add Slack` / `add Airtable` | Edit those frontmatter fields with provided values |

---

## What this skill does NOT do

- Build the full Octave library — that's `octave-context-builder`
- Push to GitHub — file stays local until Mike commits
- Send anything to the client — pure read-only intake
- Replace human judgment on ICP/offer accuracy — review gate is mandatory

---

## Failure modes

| If… | Then… |
|------|-------|
| URL is unreachable | STOP. Tell Mike, ask if there's an alternate URL. |
| Firecrawl returns empty content | STOP. Sites that aggressively bot-block aren't worth fighting at this layer. Tell Mike, suggest manual context entry. |
| Perplexity returns nothing | Continue. Leave "Recent context" empty with date stamp. |
| Template file is missing | STOP. Tell Mike `clients/_TEMPLATE.md` is gone — that's a repo integrity issue. |

---

## Connects to

- `clients/_TEMPLATE.md` — source template
- `octave-context-builder` — likely the next skill to run after this one
- Firecrawl MCP — scrape execution
- Perplexity MCP — news enrichment

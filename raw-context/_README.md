# raw-context/

The inbox. Anything new that hasn't been sorted yet goes here. Lower the barrier to capturing — sort later.

## What lives here

- **`transcripts/`** — call transcripts (sales calls, client calls, internal sessions). One file per call.
- **`briefs/`** — research briefs, strategy docs, scraped content, loom video URLs with notes
- **Root of `raw-context/`** — anything that doesn't fit into a subfolder yet. Quick captures.

## Naming convention

`YYYY-MM-DD-<short-slug>.md` — keeps everything sortable by date.

## Triage cadence

Run `/inbox-sweep` weekly. The sweep:

1. Reads every file in `raw-context/` (recursively)
2. Proposes a move plan: into `wiki/` (institutional knowledge), `clients/<name>.md` (per-client), or `archive/` (interesting but no longer current)
3. Waits for approval before moving anything
4. Never auto-deletes

## Don't put these here

- Credentials, API keys, anything sensitive — those live in `.env` and never get committed
- Final/published content — that goes in `wiki/` or `clients/` directly
- Anything you'd be sad to lose if `raw-context/` got wiped — sort it now, not later

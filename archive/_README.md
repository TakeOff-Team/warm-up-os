# archive/

Old content that's too valuable to delete but no longer current.

## What lives here

- **`archive/clients/<name>/`** — churned clients (move from `clients/`)
- **`archive/skills/<name>/`** — deprecated skills (move from `.claude/skills/`)
- **`archive/campaigns/<name>/`** — wound-down campaigns worth looking back on
- **`archive/playbooks/<name>/`** — old methodologies that got replaced (useful for context on why we changed)

## Conventions

- When archiving, preserve the original folder structure inside `archive/`
- Add a `_ARCHIVED.md` file in the archived folder with: date archived, why, what replaced it
- Never delete from `archive/` — that's the whole point of having it

## Don't put these here

- Anything still active or referenced anywhere
- Temporary / scratch work — that goes in `raw-context/`
- Anything sensitive — sensitive content gets fully removed, not archived

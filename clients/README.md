# clients/

One markdown file per active client. Onboard a new client by copying `_TEMPLATE.md` → `clients/<client-name>.md` and populating.

## Conventions

- File name: `<client-slug>.md` (lowercase, hyphenated, matches Octave library slug)
- Status managed via frontmatter `status:` field — `active`, `paused`, `churned`, `prospecting`
- Churned clients get moved to `archive/clients/<name>/` — don't delete

## Quick start for a new client

1. Copy `_TEMPLATE.md` → `clients/<client-name>.md`
2. Run `/client-context-from-url https://<client-website>` — populates "Company snapshot," "Their ICP," "Their offer," "Recent context" automatically
3. Manually add: Slack channel, Airtable record, key team, decision maker
4. Once Octave library exists for this client, link it in the frontmatter

## Future shape (v1.0)

When clients have enough work attached, this folder evolves from `clients/<name>.md` to:

```
clients/<name>/
├── CLAUDE.md          # what's currently clients/<name>.md
├── campaigns/         # one folder per campaign
├── copy/              # reusable copy patterns for this client
├── lists/             # contact lists (gitignored if sensitive)
└── reports/           # client-facing reports
```

The flat .md version is v0.5. Stay flat until the per-client volume justifies the upgrade.

# Warm-Up-OS Index

Content catalog. If you don't know where something is, search here first.

---

## By topic

| Topic | Where it lives |
|-------|----------------|
| How to start a new build (plan first) | `PLANNING.md` |
| What Warm Up actually does | `wiki/services.md` |
| Who Warm Up sells to | `wiki/icp.md` |
| Cold outreach methodology | `wiki/frameworks.md` (cold-outreach section) |
| Octave context structure | `wiki/frameworks.md` (octave section) |
| Team roles + AI insertion points | `wiki/team-roles.md` |
| Stack (Clay, HeyReach, Instantly, Octave, etc.) | `wiki/tooling.md` |
| Per-client context | `clients/<name>.md` |
| Per-client template | `clients/_TEMPLATE.md` |
| Active campaigns per client | `clients/<name>.md` → campaigns section |

---

## By client

| Client | File | Status |
|--------|------|--------|
| Sustainment | `clients/sustainment.md` | Active |
| Wabash Plastics | `clients/wabash-plastics.md` | Active (newest, signed May 2026) |
| _add more here as onboarded_ | | |

---

## By skill

| Skill | Location | What it does |
|-------|----------|--------------|
| warm-up-os-setup | `.claude/skills/warm-up-os-setup/SKILL.md` | First-run wizard — interviews you through MCPs, keys, wiki, and clients. Resumable via `.claude/setup-state.json` |
| client-context-from-url | `.claude/skills/client-context-from-url/SKILL.md` | Scrapes a client website, builds Octave-shaped context file in `clients/<name>.md` |
| octave-context-builder | `.claude/skills/octave-context-builder/SKILL.md` | Builds full Octave library structure (segments, personas, products) from client materials |
| cold-outreach-orchestrator | `.claude/skills/cold-outreach-orchestrator/SKILL.md` | End-to-end cold outreach campaign builder (Octave → list → copy → review → push) |

---

## By workflow

| Workflow | Skills involved | Entry point |
|----------|-----------------|-------------|
| First-time setup | warm-up-os-setup | `/warm-up-os-setup` |
| Onboard new client | client-context-from-url → octave-context-builder | `/client-context-from-url <url>` |
| Run cold outreach campaign | cold-outreach-orchestrator → defroster | `/cold-outreach-orchestrator` |
| Update client context after a call | (manual) edit `clients/<name>.md`, drop transcript in `raw-context/transcripts/` | — |

---

## Where things go when they arrive

| Type of input | Lands in |
|---------------|----------|
| Sales call transcript | `raw-context/transcripts/` |
| Client kickoff brief | `raw-context/briefs/` |
| Loom video from a team member | `raw-context/briefs/` with URL pasted in |
| Random idea / one-liner | `raw-context/` root (sweep weekly) |
| Finished playbook / framework | `wiki/frameworks.md` |
| New client | `clients/<name>.md` from `_TEMPLATE.md` |
| Old client who churned | `archive/clients/<name>/` |
| Deprecated skill | `archive/skills/<name>/` |

---

## Maintenance

Run `/inbox-sweep` weekly to triage `raw-context/`. Move what's useful into `wiki/` or `clients/`. Archive the rest.

When this index gets out of date, update it. Anyone can edit `INDEX.md`. It's the directory, not the content.

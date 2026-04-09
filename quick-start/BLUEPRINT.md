---
atlas_tier: framework
---

# Workspace Blueprint

Your AI reads this file to understand where everything lives. Keep it updated as your workspace grows.

---

## Structure

```text
[your-workspace]/
├── CLAUDE.md              # AI instructions (what to do, how to behave)
├── BLUEPRINT.md            # This file — the AI's map
├── knowledge/              # Durable reference material by topic
│   └── decisions/          # Decision records with rationale
├── projects/               # Active project folders
├── drafts/                 # Work in progress
├── archive/                # Completed or deprecated work
└── templates/              # (optional) Document templates
```

---

## Content Routing

| Content type | Where it goes | Example |
| --- | --- | --- |
| Something worth knowing long-term | `knowledge/[topic]/` | A framework, a pattern, a how-to |
| A deliberated choice | `knowledge/decisions/` | Tool selection, architecture decision |
| Active project work | `projects/[name]/` | Analysis, deliverables |
| Rough thinking | `drafts/` | Explorations, AI output to review |
| Done work | `archive/` | Finished projects, old drafts |

---

## Access Rules

| Path | Access | Notes |
| --- | --- | --- |
| `knowledge/` | Read/Write | AI drafts, human promotes |
| `projects/` | Read/Write | Active work |
| `drafts/` | Read/Write | Scratch space |
| `archive/` | Read-only | Don't modify archived work |

---

## Customizing

Add folders as your workspace grows. Update this file every time you change the structure. Your AI relies on this map — if the blueprint is wrong, the AI navigates wrong.

**Common additions:**

- `clients/` — client-specific work
- `research/` — investigations and analysis
- `references/` — external material you've collected
- `templates/` — document skeletons (see [Open Atlas](https://github.com/tsudo/open-atlas) for a starter set)

---

*Part of [Open Atlas](https://github.com/tsudo/open-atlas) — want the full workspace with templates, schemas, and onboarding? See the repo.*

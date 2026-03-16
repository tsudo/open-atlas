# Workspace Blueprint

Your AI's map of this workspace. This file tells agents where everything lives, what it means, and how content flows through the system.

**Keep this file updated.** When you add folders, change routing, or reorganize — update this blueprint. Your AI reads this file to navigate your workspace instead of searching blindly.

---

## Workspace Structure

```
[your-workspace]/
├── governance/
│   └── taxonomy.md                # Term definitions — what "persona", "skill", "template" mean
├── templates/
│   ├── prompt/                    # Thinking patterns — analysis, decision, extraction
│   ├── doc/                       # Document skeletons — decision records, KNOs, briefs
│   └── schema/                    # Metadata standards — frontmatter, knowledge objects
├── personas/
│   └── per_open-atlas.md          # Workspace advisor persona (example + onboarding guide)
├── context/
│   └── human-operating-model.md   # How you think and work — written for your AI to read
├── projects/
│   └── [project-name]/            # Active projects with their own analysis/ and outputs/
├── knowledge/
│   └── [topic]/                   # Durable reference knowledge (KNOs) organized by domain
├── drafts/                        # Work in progress — AI outputs, explorations, rough thinking
└── archive/                       # Completed or deprecated work — retained, not active
```

---

## Content Routing

When your AI creates or encounters content, this table tells it where to put it.

| Content type | Destination | Example |
| --- | --- | --- |
| Durable reference knowledge | `knowledge/[topic]/` | A framework, a pattern, a how-to |
| Decision record | `knowledge/decisions/` or project `analysis/` | Architecture choice, tool selection |
| Active project work | `projects/[name]/analysis/` | Research, drafts, working notes |
| Project deliverables | `projects/[name]/outputs/` | Final artifacts ready to use |
| Work in progress | `drafts/` | Rough thinking, AI explorations |
| Completed work | `archive/` | Done projects, deprecated docs |

---

## Access Rules

| Path | Access | Why |
| --- | --- | --- |
| `governance/` | Read-only | Authority layer — don't modify without explicit instruction |
| `templates/` | Read/Write | Iterate freely — templates evolve |
| `personas/` | Read/Write | Add or adjust behavioral profiles |
| `context/` | Read/Write | Update as your preferences change |
| `knowledge/` | Read/Write | AI drafts, human promotes to reviewed/canonical |
| `projects/` | Read/Write | Active work |
| `drafts/` | Read/Write | Scratch space |
| `archive/` | Read-only | Reference only — don't modify archived work |

---

## How to Customize This Blueprint

This file is a starting point. As your workspace grows:

1. **Add folders** for your domain — `clients/`, `research/`, `writing/`, whatever fits your work
2. **Update the routing table** when you add new content types
3. **Add access rules** for sensitive areas (credentials, personal docs)
4. **Point your AI here** — reference this file in your system prompt or CLAUDE.md so it knows to check the map first

The goal: your AI should never have to guess where something lives.

---

*Part of [Open Atlas](https://github.com/tsudo/open-atlas) — a free AI workspace framework.*

---
atlas_tier: framework
---

# Quick Start — Two-File Minimum

Don't want the full workspace? Start with just two files.

> **Relationship to the v1.1 minimum-viable-adoption path:** This is the *radically* minimal path — two files, no workspace structure, five minutes of setup. The v1.1 minimum-viable-adoption path in [`docs/day-1/core-concepts.md`](../docs/day-1/core-concepts.md) is the *just-enough* path — full workspace, but you only learn two of the five concepts to start. Pick this page if you want the absolute floor; pick day-1/core-concepts.md if you want the floor of the *full* workspace experience without learning everything at once.

## What You Get

| File | Purpose |
| --- | --- |
| `AI-INSTRUCTIONS.md` | Tells your AI how to behave — document standards, routing rules, behavioral guidelines |
| `BLUEPRINT.md` | Tells your AI where things live — your workspace map |

## Setup

1. Copy `AI-INSTRUCTIONS.md` and `BLUEPRINT.md` into your project root
2. **Wire it to your AI tool** — rename or copy the instructions file so your tool loads it automatically:

   | Tool | Copy `AI-INSTRUCTIONS.md` to | Auto-loaded? |
   | --- | --- | --- |
   | **Claude Code** | `.claude/CLAUDE.md` | Yes |
   | **Codex** | `AGENTS.md` (repo root) | Yes |
   | **Cursor** | `.cursorrules` (repo root) | Yes |
   | **Other tools** | Reference in your system prompt or project config | Manual |

3. Create the starter folders: `mkdir -p knowledge/decisions projects drafts archive`
4. Edit `BLUEPRINT.md` to match your actual folder structure
5. Replace `[your-name]` in the instructions file with your name
6. Start working — your AI reads both files automatically

That's it. Two files, four folders, five minutes.

## When to Upgrade

If you find yourself wanting templates, schemas, personas, or a knowledge lifecycle — you've outgrown the two-file starter.

**What the full workspace adds:**

- **Prompt templates** — structured thinking patterns for analysis, decisions, and knowledge extraction
- **Document templates** — skeletons for decision records, project briefs, research notes, procedures
- **Schemas** — frontmatter standards and knowledge object lifecycle (draft → reviewed → canonical)
- **Persona** — an AI advisor that knows the system and guides you through it
- **Human operating model** — a file that teaches your AI how you think and work

**To upgrade:** Copy the [full workspace/](../workspace/) folder over your existing setup. Your `knowledge/`, `drafts/`, and `projects/` folders stay as-is — the workspace adds structure around them, not on top of them.

---

*Part of [Open Atlas](https://github.com/tsudo/open-atlas) by [Keith Crawford](https://keithcrawford.me).*

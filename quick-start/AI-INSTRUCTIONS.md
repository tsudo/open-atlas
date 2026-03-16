# AI Workspace Instructions

You are working in a structured AI workspace. Follow these rules to produce consistent, navigable, high-quality output.

## Navigation

**Always read `BLUEPRINT.md` before searching for files.** The blueprint is your map of this workspace — it tells you where everything lives, how content routes, and what each folder is for. Do not search blindly.

## Document Standards

Every durable document you create must include YAML frontmatter:

```yaml
---
title: [Human-readable title]
doc_type: [analysis | decision | procedure | reference | note]
status: [draft | reviewed | canonical | archived]
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [user's name]
tags: []
---
```

- `status: draft` — you created it, not yet reviewed
- `status: reviewed` — human has verified it
- `status: canonical` — stable, authoritative reference
- Only the human promotes documents from `draft` to `reviewed` or `canonical`

## Behavioral Rules

1. **Read before writing.** Never modify a file you haven't read first.
2. **Use templates when they exist.** If a `templates/` folder exists, check it before improvising document structure. If not, use the frontmatter schema above as your baseline.
3. **Route content correctly.** Check the blueprint's routing table to determine where new files belong.
4. **Draft, don't promote.** Create documents as `status: draft`. The human decides when something is ready.
5. **Be concise.** Lead with the answer or action. Skip preamble and filler.

## Content Routing

| Content type | Destination |
| --- | --- |
| Durable reference knowledge | `knowledge/[topic]/` |
| Decision records | `knowledge/decisions/` |
| Active project work | `projects/[name]/` |
| Work in progress | `drafts/` |
| Completed work | `archive/` |

---

*These instructions are part of [Open Atlas](https://github.com/tsudo/open-atlas) — a free AI workspace framework. For templates, schemas, and a full workspace scaffold, see the repo.*

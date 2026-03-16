# Knowledge Object Schema

Defines durable, AI-actionable knowledge objects — the reference material your workspace builds over time.

---

## What Is a Knowledge Object?

A knowledge object (KNO) is a document that captures something worth knowing long-term: a framework, a decision pattern, a technical insight, a domain concept. Unlike meeting notes or research drafts, knowledge objects are designed to be found, read, and used months or years after they're written.

---

## Object Types

Start with these categories and add more as needed:

- `decision` — a deliberated choice with rationale
- `project-brief` — a scoped project definition
- `research-note` — an investigation with findings
- `meeting-notes` — a capture of a discussion
- `playbook` — a scenario-driven operational guide
- `recipe` — a repeatable how-to

---

## Required Sections

Every knowledge object needs at minimum:

1. **Thesis** — one sentence stating the durable claim
2. **Sources / References** — where the knowledge came from

The full recommended structure is in `templates/doc/knowledge-object.md`.

---

## Lifecycle

```
draft → reviewed → canonical → archived
```

| Status | Meaning |
| --- | --- |
| `draft` | Created, not yet verified |
| `reviewed` | Checked for correctness and completeness |
| `canonical` | Preferred reference — stable, authoritative |
| `archived` | No longer active, retained for history |

**Promotion rule:** AI may draft knowledge objects. Humans promote to `reviewed` or `canonical`. No automated promotion.

---

## Naming Convention

- `slug.md` — for evergreen references (frameworks, how-tos, patterns)
- `YYYY-MM-DD-slug.md` — for time-based objects (decisions, meeting notes)

Organize by topic in subdirectories: `knowledge/security/`, `knowledge/engineering/`, etc.

---

## Required Frontmatter

```yaml
---
title: [Title]
doc_type: kno
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [your-name]
tags: []
source: human | ai-assisted | external
source_links:
  - [url-or-path]
confidence: evolving
---
```

See `templates/schema/frontmatter.md` for the full field reference.

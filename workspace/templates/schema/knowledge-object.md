---
title: Knowledge Object Schema
doc_type: schema
status: reviewed
created: 2026-03-15
updated: 2026-04-07
owner: open-atlas
tags: [schema, knowledge-object, kno, lifecycle]
atlas_tier: framework
---

# Knowledge Object Schema

Defines durable, AI-actionable knowledge objects — the reference material your workspace builds over time.

---

## What Is a Knowledge Object?

A knowledge object (KNO) is a document that captures something worth knowing long-term: a framework, a decision pattern, a technical insight, a domain concept. Unlike meeting notes or research drafts, knowledge objects are designed to be found, read, and used months or years after they're written.

## What Counts as a KNO

Use this rubric. If you can answer "yes" to all four, it's a KNO. If not, it belongs in a different template.

1. **Durable beyond a single project.** The knowledge applies to work you'll do next month, next quarter, and next year — not just to the current project.
2. **Reference-able by future work.** You expect to grep for it, link to it, or load it into context when working on related problems.
3. **Describes a pattern, framework, decision, or method** — not a transient artifact. KNOs are the *lessons*; the *artifacts* (analyses, decisions, meeting notes) live in their own templates.
4. **Earns its place through repeated reference.** A draft becomes a real KNO when you've gone back to it more than once. If it's never been re-read, it's a draft, not a KNO.

The hardest part of running a KNO library is **not promoting things to KNOs that don't deserve it.** Most notes are not KNOs. Most decisions are not KNOs. Most analyses are not KNOs. Be ruthless — if everything is a KNO, nothing is.

## Non-KNO Doc Types

The following document types have their own templates and should NOT be promoted to KNOs:

- **Decision records** → `templates/doc/decision-record.md`. A decision is a transient choice with a date and a context. The *pattern* you derive from making many decisions might be a KNO. The decision itself is not.
- **Meeting notes** → `templates/doc/meeting-notes.md`. Meeting notes are time-stamped captures, not durable references. If a meeting produces a decision worth keeping, the decision goes in a decision record; if it produces a pattern worth keeping, the pattern goes in a KNO.
- **Project briefs** → `templates/doc/project-brief.md`. A project brief scopes a single project and stops being relevant when the project ends.
- **Research notes** → `templates/doc/research-note.md`. Research is exploratory by definition. Promote the *findings* to KNOs only when they've stabilized and you've used them more than once.
- **Procedures / playbooks** → `templates/doc/procedure.md` or `templates/doc/playbook.md`. Step-by-step operational instructions are their own thing.
- **Drafts in progress** → `workspace/drafts/`. Anything you're still figuring out. Promote when stable.
- **One-off analyses** → `workspace/drafts/` or a project folder. Most analyses don't earn KNO status.

## KNO Object Type Tags

KNOs themselves are typed by what kind of durable knowledge they capture. Start with these and add more as needed:

- `framework` — a way of thinking about a domain (e.g., risk-tier governance, three-legged stool)
- `pattern` — a recurring structure or approach (e.g., gate / steward / workflow / utility)
- `concept` — a defined term with implications (e.g., "output contract")
- `technique` — a repeatable method (e.g., "fresh-eyes adversarial review")
- `mental-model` — a way of seeing a problem (e.g., "cost of complexity exceeds cost of simplicity")
- `principle` — a durable rule that informs decisions (e.g., "trust the artifact, not the summary")

If your KNO doesn't fit one of these, ask whether it's actually a KNO or whether it belongs in a different template.

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

## Confidence Scoring (Optional)

KNOs can optionally declare a confidence dimension in frontmatter to signal how settled the knowledge is. This is independent of the lifecycle state — confidence captures *how sure you are*, while lifecycle captures *how reviewed it is*.

```yaml
confidence: high | medium | low
```

| Level | Meaning |
| --- | --- |
| `high` | Battle-tested, multiple uses, no contradictions surfaced. You'd defend this in a meeting. |
| `medium` | Working knowledge, may be revised. You believe it but haven't stress-tested it. |
| `low` | Early synthesis, expect changes. You wrote it down so you wouldn't lose the thread, but you'd hedge if asked. |

This field is optional — omit it if confidence isn't a useful signal for the KNO. The lifecycle state (`draft → reviewed → canonical`) is the primary maturity signal; confidence is a secondary dimension for KNOs whose certainty matters separately from their review status.

---

## Naming Convention

- `slug.md` — for evergreen references (frameworks, how-tos, patterns)
- `YYYY-MM-DD-slug.md` — for time-based objects

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
confidence: high | medium | low  # optional
---
```

See `templates/schema/frontmatter.md` for the full field reference.

---
atlas_tier: framework
title: "Decision: Use Markdown for All Workspace Documents"
doc_type: decision
status: reviewed
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: alex
tags: [tooling, workflow, foundational]
---

> **Sample decision record showing a filled-in decision template.** This file demonstrates the decision-record format. For how the v1.1 production skills help you work through a decision *before* recording it, see [`workspace/skills/think-it-through.md`](../workspace/skills/think-it-through.md) — it composes the `options-and-tradeoffs` strategy with anti-sycophancy gates and produces a structured decision-thinking artifact you can capture into a record like this one.

## Context

Setting up a new AI workspace requires choosing a document format. The team uses multiple tools (VS Code, Obsidian, GitHub) and wants documents that are readable everywhere, version-controllable, and parseable by AI agents.

**Triggered by:** Workspace setup — need to commit to a format before creating templates.

---

## Options Considered

| Option | Summary | Pros | Cons |
| --- | --- | --- | --- |
| A: Markdown | Plain text with lightweight formatting | Universal tooling, Git-friendly, AI-readable | No rich media embedding, limited layout |
| B: Notion / Google Docs | Cloud-based rich documents | Rich formatting, collaboration | Vendor lock-in, not Git-friendly, AI access requires API |
| C: JSON / YAML files | Structured data | Machine-readable, schema-enforceable | Not human-friendly for long-form content |

---

## Decision

**Decision:** Use Markdown with YAML frontmatter as the standard format for all workspace documents.

**Decided by:** Alex

**Date decided:** YYYY-MM-DD

**Status:** reviewed

---

## Rationale

Markdown is the only format that works natively across all our tools — VS Code renders it, Obsidian is built on it, GitHub displays it, and every AI agent can read it without an API. YAML frontmatter adds the machine-readable metadata layer without sacrificing human readability. Notion was rejected because it creates a dependency on a specific platform and makes Git-based version control impractical.

---

## Risks and Tradeoffs

**Accepted tradeoffs:** No rich media layout. Complex tables are awkward. Collaboration requires Git workflow rather than real-time editing.

**Residual risks:**

- **Formatting drift:** Different tools render markdown slightly differently — mitigated by sticking to CommonMark spec
- **Frontmatter inconsistency:** Without enforcement, documents may have inconsistent metadata — mitigated by templates with pre-filled frontmatter

---

## Follow-ups

| Action | Owner | Due | Status |
| --- | --- | --- | --- |
| Create frontmatter schema | Alex | YYYY-MM-DD | [x] |
| Set up document templates | Alex | YYYY-MM-DD | [ ] |

---

## Related Decisions

- None identified — this is a foundational decision.

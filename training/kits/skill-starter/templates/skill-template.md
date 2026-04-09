---
atlas_tier: framework
title: "[Skill Name]"
doc_type: skill
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [your-name]
tags: [skill]
function: workflow
hotwords: []
---

<!--
SKILL TEMPLATE — Open Atlas v1.1 (kit-local copy)

This is a copy of workspace/templates/skill/skill-template.md, kept inside the
skill-starter kit so the kit is self-contained. The canonical version lives at
workspace/templates/skill/skill-template.md — both copies should stay in sync.

Fill in the bracketed sections. Delete this comment block when done.

When you (or the kit's SETUP.md) create a skill FROM this template, the new file
should set `atlas_tier: user` in its frontmatter — not `framework`.
-->

# Skill: [Name]

**Function:** [gate | steward | workflow | utility]
**Composes strategy:** [pick one from `workspace/templates/strategy/` if your skill does reasoning work, or omit for pure utilities]
**Composes contract:** [base output contract](../../../../workspace/templates/output-contract/base.md)
**Hotwords:** `[trigger 1]`, `[trigger 2]`

---

## Purpose

[2-3 sentences. What this skill is for.]

---

## What This Skill Does

- [Action 1]
- [Action 2]
- [Action 3]

---

## What This Skill Does NOT Do

- [Non-responsibility 1]
- [Non-responsibility 2]
- [Non-responsibility 3]

---

## Inputs

- **Required:** [input 1]
- **Optional:** [input 2]

---

## Procedure

1. [Step 1]
2. [Step 2]
3. [Step 3]

---

## Output Contract

**Composes:** [base output contract](../../../../workspace/templates/output-contract/base.md)

**Required body sections** (in this order):

- `## [Section name 1]` — [one-line description]
- `## [Section name 2]` — [one-line description]
- `## [Section name 3]` — [one-line description]

**Status mapping:**

- `DONE` — [the condition under which this skill returns DONE]
- `DONE_WITH_NOTES` — [the condition for DONE_WITH_NOTES]
- `BLOCKED` — [the condition for BLOCKED]
- `NEEDS_CONTEXT` — [the condition for NEEDS_CONTEXT]

The base contract handles the universal envelope (frontmatter, summary, notes, status). Define only what's unique to this skill above.

---

## NO-HEDGING Contract

[Delete this section if function != gate.]

Forced verdicts: `PASS | PASS-WITH-NOTES | BLOCK | SKIPPED`. Every finding cites specific evidence. Skill cannot silently bypass its own checks.

---

## Event Emission

[Delete this section if function is workflow or utility.]

Emits POSITIVE_SIGNAL events on: [`worked_as_designed`, `caught_real_issue`, `first_use`, etc.]

---

## Activation

- **Hotword(s):** `[trigger]`
- **Explicit invocation:** `Run the [Name] skill`

---

## Authority Boundaries

- [What this skill can and cannot do]

---

## Resources

- `workspace/templates/strategy/` — five reasoning strategy templates skills can compose
- `workspace/templates/output-contract/base.md` — the base output envelope this skill composes
- `workspace/governance/skill-conventions.md`
- `docs/reference/skill-functions.md`
- `docs/reference/feedback-loop.md`
- `docs/reference/output-contracts.md`

---

*Created from `workspace/templates/skill/skill-template.md` (Open Atlas v1.1).*

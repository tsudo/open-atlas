---
atlas_tier: framework
title: Base Output Contract
doc_type: output-contract-template
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [output-contract, base-template, composability]
---

# Base Output Contract

The universal envelope every Open Atlas skill output uses. Composed by the four production skills in `workspace/skills/` and by any user-built skill that follows the convention.

**When to compose:** Default for any skill that produces a structured artifact. Use this unless you have a specific reason to define your own envelope.

**When NOT to compose:** Rare. The base contract is general enough for workflow, steward, gate, and utility skills. Roll your own only if your skill produces something fundamentally different (e.g., a binary file, an interactive flow, a non-markdown artifact).

---

## The envelope

Every output that composes the base contract has this shape:

```markdown
---
skill: [skill-name]
invoked_at: [YYYY-MM-DD or timestamp if precision matters]
inputs: [one-line summary of what the skill was given]
status: [DONE | DONE_WITH_NOTES | BLOCKED | NEEDS_CONTEXT]
status_why: [one sentence explaining the status, required]
---

# [Skill name] — Output

## Summary

[1-2 sentences. The headline result. What the skill did and what the user gets.]

## [Skill-specific section 1]

[Content per the skill's procedure]

## [Skill-specific section 2]

[Content per the skill's procedure]

## [Additional skill-specific sections as needed]

## Notes

[Caveats. Anything the skill couldn't do. Things the user should know before
acting on the output. Omit this section cleanly if there's nothing to note —
do not write "N/A" or "no notes" filler.]
```

The frontmatter `status` field carries the completion state. There is no footer restating it — the frontmatter is the single source of truth.

---

## Required elements

These are non-negotiable. A contract that omits any of these is not a base contract.

| Element | Required content |
|---|---|
| **Frontmatter `skill`** | The skill name as it appears in the skill file's frontmatter |
| **Frontmatter `status`** | One of the four canonical values (see below). This is the single source of truth for completion state — no footer restatement needed. |
| **Frontmatter `status_why`** | One sentence explaining the status. Required even when status is `DONE` — a `DONE` without an explanation looks like hedging by claiming success. |
| **`# [Skill name] — Output` header** | The H1 — names what produced the output |
| **`## Summary`** | 1-2 sentences. Not optional. The headline result for users who only read the top of the output. |

---

## Optional elements

These appear when the skill needs them and are omitted cleanly when it doesn't.

| Element | When to include |
|---|---|
| **`invoked_at` frontmatter** | When timing matters (audits, time-bounded reports, anything cadence-related) |
| **`inputs` frontmatter** | When the user needs to remember what the skill was given to interpret the output |
| **`## Notes` section** | Only when there's something genuine to note. Empty notes are filler. |
| **Skill-specific body sections** | The skill's `## Output Contract` section names which sections go inside the envelope |

---

## Completion status vocabulary

The `status` value in frontmatter and the status footer must be one of these four:

| Status | Meaning |
|---|---|
| **`DONE`** | Skill ran cleanly to completion. Output produced as the contract promised. No caveats that matter. |
| **`DONE_WITH_NOTES`** | Skill ran but encountered something worth surfacing without failing. The Notes section explains. |
| **`BLOCKED`** | Skill cannot complete. The Notes section explains the specific reason. The body may be partial or empty. |
| **`NEEDS_CONTEXT`** | Skill cannot start. Missing input or ambiguous instruction. The Notes section names the specific clarification needed. |

These four are the entire vocabulary. Coining a fifth value (`MOSTLY_DONE`, `PARTIAL`, etc.) is a contract violation. If you need finer gradation, use `DONE_WITH_NOTES` and explain the gradation in the Notes section.

---

## Discipline rules

These rules apply to any skill output that composes this contract. They're enforceable by reading the output and checking for compliance — no tooling needed.

1. **The Summary section is for the headline, not the methodology.** If the user only reads two sentences, those two sentences should tell them what they got, not how the AI got there.

2. **The Notes section is for things that change how to read the output.** Caveats, partial completions, things the AI couldn't verify, files that didn't load. NOT for restating the procedure or padding the output.

3. **Empty optional sections are omitted, not filled with placeholder text.** If there's nothing to note, the Notes section doesn't appear. If there are no caveats, don't write "no caveats."

4. **The `status_why` frontmatter field is required even when status is `DONE`.** A `DONE` without an explanation looks like hedging by claiming success. One sentence is enough: "All sections completed and the recommended default has high confidence."

5. **Skill-specific body sections appear in the order the skill file lists them.** The skill's `## Output Contract` section is the source of truth for section order.

---

## How a skill references this contract

In the skill file's `## Output Contract` section:

```markdown
## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections** (in this order):

- `## [Section name 1]` — [one-line description of what goes here]
- `## [Section name 2]` — [one-line description]
- `## [Section name 3]` — [one-line description]

**Status mapping:**

- `DONE` — [the condition under which this skill returns DONE]
- `DONE_WITH_NOTES` — [the condition for DONE_WITH_NOTES]
- `BLOCKED` — [the condition for BLOCKED]
- `NEEDS_CONTEXT` — [the condition for NEEDS_CONTEXT]
```

That's it. The skill defines its body sections and its status mapping. The envelope is inherited.

---

## Why this template exists

Three reasons:

1. **Consistency.** Users should be able to read any skill output and find the status, the summary, and the notes in the same places. Predictability reduces cognitive load.

2. **Composability.** Skills that compose the base contract can be terser. They define what's unique; the envelope is shared.

3. **Improvement compounds.** When the envelope improves (clearer status definitions, better summary discipline), the improvement applies to every skill at once instead of being copy-pasted into every skill file.

This is the same architecture that the strategy templates in `workspace/templates/strategy/` use for reasoning. Compose, don't reinvent.

---

## Resources

- [`workspace/templates/output-contract/README.md`](README.md) — orientation to this directory
- [`docs/reference/output-contracts.md`](../../../docs/reference/output-contracts.md) — the discipline behind output contracts (forced verdicts, evidence requirements, completion status protocol)
- [`workspace/templates/strategy/`](../strategy/) — the parallel concept for reasoning
- [`workspace/skills/`](../../skills/) — production skills that compose this contract

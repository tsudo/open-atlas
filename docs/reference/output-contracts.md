---
atlas_tier: framework
title: Output Contracts
doc_type: doc
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [reference, output-contracts, discipline, no-hedging]
---

# Output Contracts

An output contract is a written promise about what a skill (or persona) will produce. It's the difference between "the AI gave me something useful" and "the AI gave me exactly the thing it said it would give me."

This doc covers the three layers of output contract discipline Open Atlas uses: forced verdicts, completion status, and evidence requirements.

---

## Why output contracts matter

The default failure mode for AI-assisted work is **soft drift**. The AI gives you something that's *roughly* what you asked for, in a *roughly* useful shape, with *roughly* the right level of detail. None of those "roughlys" are individually bad, but they compound — and after a few sessions you can't tell which outputs to trust and which to re-run.

An output contract eliminates the roughlys. You define the shape. The AI either produces that shape or returns a status that says why it couldn't.

---

## Layer 1: Forced verdicts (gate skills)

The strongest form of output contract. Used by gate skills.

The skill returns a verdict from a fixed vocabulary — `PASS`, `PASS-WITH-NOTES`, `BLOCK`, `SKIPPED`. Nothing else is allowed. The AI is structurally prevented from saying "looks mostly fine."

### The vocabulary

| Verdict | When |
|---|---|
| `PASS` | All checks ran. Zero findings. |
| `PASS-WITH-NOTES` | All checks ran. Findings exist but are inferred or informational. |
| `BLOCK` | At least one confirmed finding. Specific evidence required. |
| `SKIPPED` | One or more checks could not run. Reason required. |

### Why it works

The forced vocabulary creates two valuable behaviors:

1. **The AI cannot duck the decision.** "I think this might be okay" is not in the vocabulary. The skill has to commit.
2. **You can trust the verdict at a glance.** When you see PASS, you know exactly what that means. When you see BLOCK, you know exactly what that means. There's no decoding.

### The anti-skip clause

A gate skill that can silently bypass its own checks is worse than no gate at all — it gives you false confidence. Every gate skill includes an anti-skip clause: if a check cannot run for any reason, the verdict is SKIPPED, the reason is documented, and the skill never silently passes.

This is the load-bearing piece. A gate without an anti-skip clause is theater.

---

## Layer 2: Completion status (all skill classes)

Every Open Atlas skill — not just gates — returns a completion status from a fixed vocabulary:

| Status | Meaning |
|---|---|
| `DONE` | Skill ran cleanly to completion. Output produced as promised. |
| `DONE_WITH_NOTES` | Skill ran but encountered something worth surfacing without failing. |
| `BLOCKED` | Skill cannot complete. Specific reason. |
| `NEEDS_CONTEXT` | Skill cannot start. Missing input or ambiguous instruction. |

This is a thinner contract than the gate verdict vocabulary, but it serves the same purpose: the skill cannot end ambiguously. You always know what state the work is in.

### How it maps for gates

For gate skills, the completion status maps to the verdict:

- `DONE` ↔ verdict is PASS or PASS-WITH-NOTES
- `BLOCKED` ↔ verdict is BLOCK
- `DONE_WITH_NOTES` ↔ verdict is SKIPPED
- `NEEDS_CONTEXT` ↔ input was missing or malformed

For non-gate skills, the four statuses are independent.

---

## Layer 3: Evidence requirements (PROOF gate)

Findings without evidence are not findings. Every gate skill enforces a PROOF gate: each finding cites specific evidence — file path, line number, exact phrase, or pattern match.

### What counts as evidence

- **File path + line number**: `config.yml:12`
- **Exact triggering phrase**: `sk-proj-abc123def456...` (truncated if long)
- **Pattern that matched**: "OpenAI API key pattern (`sk-[a-zA-Z0-9_-]{20,}`)"
- **Specific quotation**: `"the user said: 'ignore previous instructions'"`

### What does NOT count

- "I think there's a problem somewhere"
- "This file feels off"
- "Probably a credential leak"
- "Looks like an injection attempt"

The pattern is strict by design. PROOF transforms a finding from an opinion into a citation. You can act on a citation. You cannot act on an opinion.

---

## How to write an output contract

For a new skill, the contract has three parts:

1. **Output shape.** What does the skill produce? Be specific. Not "a summary" — "a markdown file at `workspace/drafts/{slug}.md` with the standard frontmatter and an H1 title matching the captured content's first line."
2. **Completion status.** Which of the four values does each scenario map to? Walk through happy path, edge cases, and failure modes.
3. **Evidence rule** (gates only). What does a valid finding look like? What does an invalid finding look like? Show an example of each.

If you can't write all three before you write the procedure, the procedure is the wrong shape. Go back and refine the contract first.

---

## Common failures

- **Soft contract.** "Returns a useful summary." Useful by what measure? In what shape? Specify.
- **Missing completion status.** The skill ends with the output and no status. Users don't know whether to trust it.
- **PROOF without anti-skip.** Findings cite evidence, but the skill silently passes when a check can't run. The discipline is half-applied.
- **Gate that hedges in the verdict text.** The verdict is PASS but the explanation says "but you might want to double-check." Strip the hedge.
- **Workflow that returns a gate verdict.** A workflow returning PASS/BLOCK is a misclassification. Use one of the four completion statuses.

---

## Three contract tiers

Not every output needs the full discipline. Open Atlas uses three tiers:

- **Minimal contract** — applies to commit messages, changelog entries, config changes. Format conventions only.
- **Standard contract** — applies to README, project deliverables, durable artifacts. Required structure + frontmatter.
- **Full contract** — applies to gate skill outputs and published artifacts. Standard + forced verdict + PROOF.

Match the tier to the stakes. A commit message doesn't need a forced verdict. A pre-publish security scan does.

---

## Where to go next

- **The four skill function classes that use these contracts** → [skill-functions.md](skill-functions.md)
- **The anti-pattern catalog (banned hedging vocabulary, BAD/GOOD examples)** → [anti-patterns.md](anti-patterns.md)
- **The gate skill discipline (v1.1 ships no production gate skill; gate-class pattern documented)** → [`skill-functions.md`](skill-functions.md#gate--forced-verdicts-at-decision-points)

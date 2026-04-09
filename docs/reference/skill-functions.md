# Skill Functions

Every Open Atlas skill belongs to exactly one of four function classes. Picking the right class is the most consequential design decision when you build a skill — it determines the discipline the skill enforces, the output shape, and what kinds of failures it can have.

---

## The four classes at a glance

| Class | Purpose | Discipline | Output shape |
|---|---|---|---|
| **`gate`** | Force a verdict at a decision point | NO-HEDGING + PROOF + anti-skip | Forced verdict from a fixed vocabulary |
| **`steward`** | Periodic ongoing care of a surface | POSITIVE_SIGNAL emission + cadence | Health report with findings |
| **`workflow`** | Multi-step process from input to output | Procedure + output contract | Whatever the contract specifies |
| **`utility`** | Read-only helper | Lightweight | Whatever's useful |

If you're not sure which to pick, **default to `workflow`**. The other three are specializations.

---

## `gate` — Forced verdicts at decision points

A gate skill is invoked when you need a yes/no answer that the AI cannot duck. Pre-publish secret scans, code review checkpoints, "is this ready to ship?" reviews — anything where hedged language ("looks mostly fine") is itself a failure.

### Discipline

Three things make a skill a gate:

1. **NO-HEDGING contract.** Hedged vocabulary is forbidden in the output. No "probably," "looks like," "seems okay." The skill returns a verdict from a fixed vocabulary.
2. **PROOF gate.** Every finding cites specific evidence — file:line, exact phrase, or pattern match. "I think there's a problem somewhere" is not a finding.
3. **Anti-skip clause.** If a check cannot run (file unreadable, dependency missing, parser failed), the skill emits SKIPPED with the reason. Never silent PASS.

### The forced verdict vocabulary

Every gate skill returns one of:

| Verdict | Meaning |
|---|---|
| **`PASS`** | All checks ran. Zero findings. Specific list of what was checked. |
| **`PASS-WITH-NOTES`** | All checks ran. Findings exist but are inferred or informational, not blocking. |
| **`BLOCK`** | At least one confirmed finding makes the artifact unshippable. Specific evidence required. |
| **`SKIPPED`** | One or more checks could not run. Reason required. |

The vocabulary is fixed by design. Adding a fifth verdict ("MOSTLY-PASS") destroys the discipline.

### Canonical example

v1.1 does not ship a production gate skill. The gate-class discipline is documented above (NO-HEDGING contract, forced verdict vocabulary, anti-skip clause). v1.2 will ship a production `publish-check` gate skill alongside the working credential-leak hook so the deterministic-and-qualitative pairing is visible. Until then, use this section as the design contract if you're building your own gate skill.

### When to use

- Pre-publish or pre-ship checks
- Security scans before content leaves the workspace
- Code review checkpoints
- Anywhere you need the AI to *refuse* to be wishy-washy

---

## `steward` — Periodic ongoing care

A steward skill takes care of a surface over time. Workspace audits, drift detection, health checks, knowledge curation. Stewards are *cadence* skills — meant to run weekly or after each meaningful work session, not before every task.

### Discipline

1. **POSITIVE_SIGNAL emission.** Stewards are designed to accumulate signal across runs, so trend information becomes visible. Each run emits events with the standard qualifiers.
2. **Cadence, not on-demand.** A steward that runs on every commit is not a steward — it's a gate. If you're invoking a steward more than once a day, it's the wrong shape.
3. **Read-only.** Stewards detect and recommend. They never modify files. Promotion, archiving, and deletion are human decisions even when the steward flags candidates.

### Output shape

A health report with three sections:

- **Inventory** — what's in the surface
- **Findings** — what's drifting, with severity and recommended action
- **Trends** — what's changed since the last run (if you've run it before)

Silence is not an output. Even a clean audit produces the inventory and trends sections.

### Canonical example

[`workspace/skills/review-context.md`](../../workspace/skills/review-context.md) — production steward skill that ships in v1.1. Composes `adversarial-critique` to walk the workspace, finds drift, produces a structured health report, appends to an audit log for trend signal across runs.

### When to use

- Workspace audits
- Drift detection
- Health checks on a defined surface
- Anything that needs to look at the *whole* of something on a cadence

---

## `workflow` — Multi-step process

A workflow skill takes input, runs a defined procedure, and produces an output. This is the most common shape and the right default when you're not sure.

### Discipline

1. **Numbered procedure.** Mechanical enough that an AI can follow without judgment calls.
2. **Output contract.** What the skill produces. Specific format. Not "a summary" — "a markdown file at `workspace/drafts/{slug}.md` with the standard frontmatter."
3. **Completion status.** `DONE | DONE_WITH_NOTES | BLOCKED | NEEDS_CONTEXT`. The skill cannot end ambiguously.

### Output shape

Whatever the contract says. Workflow skills are the most flexible — the discipline comes from the procedure and the contract being explicit, not from a fixed output vocabulary.

### Canonical example

[`workspace/skills/capture.md`](../../workspace/skills/capture.md) — production workflow skill that ships in v1.1. Composes `structured-extraction`, classifies input by artifact type, routes to `workspace/drafts/`, returns the file path. The canonical first skill: high-frequency, low-discipline, default-to-drafts.

### When to use

- Repeatable multi-step processes
- "I have X, I want Y" workflows
- The default class when none of the others obviously fits

---

## `utility` — Read-only helper

A utility skill is a lightweight read-only helper. Look something up, transform a string, format a date range, find files matching a pattern. Utilities are the simplest class — no procedure section is necessary if the action is one step.

### Discipline

Minimal. The discipline of a utility is *to stay small*. A utility skill that's accumulated five sections and a procedure is not a utility — it's a workflow. Promote it.

### Output shape

Whatever's useful. A list, a number, a string, a small report.

### When to use

- Lookups
- Read-only transformations
- Small helpers you want to invoke with a hotword

---

## How to pick the right class

Ask three questions in order:

1. **Does this end in a forced yes/no verdict where hedging would be a failure?** → `gate`
2. **Is this a periodic check on the health of a surface?** → `steward`
3. **Is this a multi-step process from input to output?** → `workflow`

If none of those is true, you have a `utility` (or you don't need a skill at all — it might just be a one-off prompt).

---

## What happens if you pick the wrong class

- **Workflow misclassified as gate.** You force the AI to return PASS/BLOCK on something that's genuinely advisory. The forced vocabulary feels wrong, the user disables the skill.
- **Gate misclassified as workflow.** The skill hedges. Users learn to ignore its output. The discipline is gone.
- **Steward misclassified as workflow.** Users invoke it on demand and don't get the trend value. The POSITIVE_SIGNAL events accumulate but no one reads them.
- **Workflow misclassified as utility.** The skill grows beyond its frame, the file is undisciplined, the output is inconsistent.

The fix in every case: re-classify, restructure, ship the rewrite. Skill files are cheap to edit.

---

## Where to go next

- **The output contract discipline that gates enforce** → [output-contracts.md](output-contracts.md)
- **The feedback loop that stewards feed** → [feedback-loop.md](feedback-loop.md)
- **The day-2 doc on creating skills** → [../day-2/creating-skills.md](../day-2/creating-skills.md)
- **The hardened skill template** → `workspace/templates/skill/skill-template.md`

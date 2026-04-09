---
atlas_tier: framework
title: "review-context"
doc_type: skill
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [skill, steward, audit, drift-detection, adversarial-critique]
function: steward
hotwords: [review context, workspace audit, health check, drift check]
---

# Skill: review-context

**Function:** steward
**Composes strategy:** [`adversarial-critique`](../templates/strategy/adversarial-critique.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `review context`, `workspace audit`, `health check`, `drift check`

---

## Purpose

Walk the workspace, find what's drifting, and produce a health report the user can act on. The "is my workspace rotting?" skill — periodic, not on-demand. Run weekly, after a meaningful work session, or when the user asks. Read-only: surfaces candidates, never promotes or deletes.

---

## Procedure

1. **Determine scope.** If the user named a subdirectory (e.g., "audit `workspace/knowledge/`"), scope to that. Otherwise default to the entire `workspace/` tree.

2. **Walk the scope.** List all `.md` files in the scoped tree. Note total count.

3. **Compose the [`adversarial-critique`](../templates/strategy/adversarial-critique.md) strategy.** The job is to find drift, not to validate that the workspace is fine. Apply the strategy's discipline:
   - Assume the workspace has problems — your job is to find them
   - For each finding, classify severity: HIGH / MEDIUM / LOW
   - State what's wrong, why it matters, and a concrete fix
   - Do not pad the report with praise
   - If you find nothing, say so and explain what you checked — an empty report with evidence of thoroughness is better than a padded report with filler

4. **Run the drift checks** in this order. Each check produces zero-or-more findings.

   **Frontmatter validation:**
   - Required fields present (`title`, `doc_type`, `status`, `created`, `updated`)
   - `status` value is one of `draft | reviewed | canonical | archived`
   - `doc_type` is one of the known types (per `workspace/templates/schema/` if available, otherwise: `note`, `decision`, `knowledge`, `action`, `reference`)
   - Severity: MEDIUM for missing required fields, LOW for invalid enum values

   **Stale drafts:**
   - Files with `status: draft` and an `updated` date older than 30 days
   - Severity: LOW (5-10 stale drafts), MEDIUM (10-25), HIGH (>25)

   **Wrong-directory placement:**
   - Files where the `doc_type` does not match the directory they live in (e.g., a `doc_type: knowledge` file in `workspace/drafts/`)
   - Severity: MEDIUM

   **Orphan canonicals:**
   - Files with `status: canonical` and an `updated` date older than 90 days
   - Severity: LOW (just flag — old canonicals may be deliberately stable, ask the user to review)

   **Empty directories:**
   - Subdirectories under `workspace/` with no `.md` files
   - Severity: LOW

5. **Aggregate counts.** Total files, files by status, files by `doc_type`, files modified in last 7 / 30 / 90 days.

6. **Produce trends if a prior audit log exists** at `workspace/.audit-log.jsonl`. Compare counts to the last audit; surface meaningful deltas (e.g., "drafts grew from 12 to 47"). If no prior log exists, skip trends and note that this is the first audit.

7. **Append a one-line entry to `workspace/.audit-log.jsonl`** with the date, total file count, finding count by severity, and the top finding category. This builds the trend signal for future runs.

8. **Return the output in the [base contract](../templates/output-contract/base.md) shape with the body sections defined below.**

---

## What This Skill Does NOT Do

- Does NOT modify any files. Read-only audit. Cannot promote, demote, archive, or delete.
- Does NOT run on every task. This is a cadence skill — weekly is the right default. Running it before every commit is wrong.
- Does NOT make subjective quality judgments. Only mechanical checks against schema and conventions. "This file is poorly written" is not a finding.
- Does NOT promote drafts. Surfaces candidates; the user decides.
- Does NOT delete or archive content. Only flags candidates.
- Does NOT create new doc types or schemas. Audits against the existing taxonomy.
- Does NOT extend the scope without permission. If the user said "audit `workspace/knowledge/`," do not also walk `workspace/drafts/`.

---

## Inputs

- **Required:** None. Default scope is the full `workspace/` tree.
- **Optional:** A scope filter (a subdirectory path).
- **Optional:** A "since date" — only flag findings introduced after this date. Useful when running a follow-up audit after the user resolved findings.

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections** (in this order):

- `## Inventory` — total files, breakdown by status, breakdown by doc_type, recently-modified counts (7d / 30d / 90d)
- `## Findings` — list of findings, ordered by severity (HIGH first). Each finding has: severity, type (e.g., `stale-draft`, `wrong-directory`, `frontmatter-invalid`), file path, one-sentence issue, recommended action
- `## Trends` — comparison to prior audit if `.audit-log.jsonl` exists; "first audit — no trends yet" otherwise
- `## Suggested Next Actions` — 2-4 bullet points the user can act on, ordered by leverage

**Status mapping:**

- `DONE` — audit ran cleanly, findings produced (or zero findings reported with evidence of thoroughness), audit log appended
- `DONE_WITH_NOTES` — audit ran but encountered files it could not parse (corrupt frontmatter, invalid YAML). Notes section lists them.
- `BLOCKED` — workspace structure is malformed (`workspace/` directory missing, or scope filter pointed at a path that does not exist)
- `NEEDS_CONTEXT` — scope filter is ambiguous (matches multiple paths or no paths). Notes section names the clarification needed.

---

## Activation

- **Hotword(s):** `review context`, `workspace audit`, `health check`, `drift check`
- **Explicit invocation:** `Read workspace/skills/review-context.md and run it on the workspace.`
- **Tool wiring:** Claude Code slash command `/review-context`; can be wired as a weekly scheduled task in tools that support scheduling
- **Cadence:** Weekly is the right default. After every task is wrong (steward becomes a gate). Once a year is too rare.

---

## Resources

- [`workspace/templates/strategy/adversarial-critique.md`](../templates/strategy/adversarial-critique.md) — the reasoning strategy this skill composes
- [`workspace/templates/output-contract/base.md`](../templates/output-contract/base.md) — the output envelope this skill composes
- [`workspace/templates/schema/`](../templates/schema/) — the frontmatter standard the audit validates against
- [`docs/day-2/workspace-maintenance.md`](../../docs/day-2/workspace-maintenance.md) — when-to-archive, when-to-promote, when-to-delete guidance for acting on findings

---

*Steward skill composing `adversarial-critique` + base output contract. Cadence-based, read-only, produces a structured health report with drift findings and trends. Surfaces candidates; the user decides.*

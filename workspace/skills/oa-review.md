---
atlas_tier: framework
title: "oa-review"
doc_type: skill
status: reviewed
created: 2026-04-13
updated: 2026-04-13
owner: open-atlas
tags: [skill, steward, audit, health-check, maintenance, drift-detection]
function: steward
hotwords: [oa review, workspace review, full audit, system check]
freedom_level: low
---

# Skill: oa-review

**Function:** steward
**Freedom level:** low
**Composes strategy:** [`adversarial-critique`](../templates/strategy/adversarial-critique.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `oa review`, `workspace review`, `full audit`, `system check`

---

## Purpose

Full structural audit of an Open Atlas workspace. Walks seven check categories looking for governance drift, broken references, stale content, and convention violations. Produces a categorized findings report the user can act on. Read-only — surfaces candidates, never modifies files.

Relationship to `review-context`: that skill is a lighter weekly drift check (frontmatter, staleness, adversarial scan of a scoped surface). This skill is the monthly or post-change full audit covering structural integrity, manifest alignment, skill health, and governance consistency across the entire workspace.

---

## What This Skill Does

- Runs seven check categories covering the full workspace governance surface
- Classifies findings by severity (HIGH, MEDIUM, LOW)
- Cites specific evidence for every finding (file path, field name, expected vs actual)
- Supports quick mode (categories 1-2 only) and full mode (all seven)
- Produces a structured report with inventory counts and findings
- Emits POSITIVE_SIGNAL on completion (steward contract)

---

## What This Skill Does NOT Do

- Modify any files — findings are recommendations, not auto-fixes
- Replace `review-context` — that skill runs weekly on scoped surfaces; this runs monthly on everything
- Check external services, URLs, or API endpoints
- Validate the content quality of knowledge objects or drafts
- Run security scans (use a dedicated security-review skill for that)

---

## Inputs

- **Required:** None (audits the workspace root by default)
- **Optional:** Mode — `quick` or `full`. Default: `quick`.

---

## Procedure

### 1. Mode selection

Check if the user specified a mode:
- `quick` → run categories 1-2 only
- `full` → run all seven categories
- Neither → default to `quick`. State: "Running quick mode (frontmatter + cross-references). Say `full` for the complete seven-category audit."

### 2. Compose adversarial strategy

Load [`adversarial-critique`](../templates/strategy/adversarial-critique.md). The job is to find problems, not validate that the workspace is healthy. Apply the strategy's discipline:
- Assume the workspace has problems — your job is to find them
- For each finding, classify severity: HIGH / MEDIUM / LOW
- State what's wrong, why it matters, and a concrete fix
- Do not pad the report with praise
- If you find nothing in a category, say so and explain what you checked

### 3. Run check categories

Run each category in order. For each finding, record: category number, severity, file path, description, recommended fix.

#### Category 1: Frontmatter Integrity (quick + full)

Walk all `.md` files under `workspace/`. For each file:

- Required fields present: `title`, `doc_type`, `status`, `created`, `updated`
- `status` is one of: `draft`, `reviewed`, `canonical`, `archived`
- `doc_type` is a known type (check `workspace/templates/schema/` if available)
- `atlas_tier` is `user` or `framework` (if present)
- Dates are valid ISO format

**Severity:** HIGH for missing `doc_type` or `status`. MEDIUM for missing other required fields. LOW for invalid enum values.

#### Category 2: Cross-Reference Integrity (quick + full)

Walk all `.md` files in the repo. For each internal markdown link `[text](path)`:

- Resolve the path relative to the linking file's directory
- Confirm the target file exists
- For anchor links `(path#anchor)`, confirm the file exists (anchor validation is optional)

**Severity:** HIGH for every broken link. This is the single most common workspace failure mode.

**Evidence:** Source file, line number (if determinable), target path, "file not found."

#### Category 3: Skill Health (full only)

Walk all `.md` files under `workspace/skills/`. For each skill:

- `function:` field present and valid (`gate`, `steward`, `workflow`, `utility`)
- `hotwords:` field present and non-empty
- `freedom_level:` field present (MEDIUM if missing — it's recommended, not required)
- No hotword collisions across skills (HIGH if two skills share a hotword)
- Procedure section exists and has numbered steps
- "What This Skill Does NOT Do" section exists
- File is under 500 lines (MEDIUM if over — per size ceiling convention)

**Severity:** HIGH for missing function or hotword collisions. MEDIUM for missing recommended fields. LOW for style issues.

#### Category 4: Manifest Alignment (full only)

Read `.atlas-manifest.yml`. Check:

- Every file under `workspace/skills/` that isn't in the user-tier glob has an explicit exception entry if it's framework-owned
- Every file listed in `framework` tier paths actually exists
- No files exist in framework-tier paths that aren't listed (orphaned framework content)
- `atlas_version` field is present

**Severity:** HIGH for framework files missing from manifest. MEDIUM for orphaned entries. LOW for missing version field.

#### Category 5: Governance Drift (full only)

Compare governance documents against actual workspace state:

- `workspace/BLUEPRINT.md` lists all directories and key files that actually exist
- `workspace/governance/taxonomy.md` terms are consistent with actual workspace usage
- `workspace/governance/skill-conventions.md` frontmatter requirements match what skills actually declare
- `workspace/governance/pipeline.md` phase and gate definitions are internally consistent

**Severity:** HIGH for BLUEPRINT listing files that don't exist. MEDIUM for taxonomy or convention drift. LOW for minor inconsistencies.

#### Category 6: Stale Content (full only)

Walk all `.md` files under `workspace/`. Flag:

- Files with `status: draft` and `updated:` date older than 30 days
- Files with `status: draft` that have never been promoted (potential abandoned drafts)
- Knowledge objects with `confidence:` below 5/10 that are older than 30 days (should be validated or retired)
- Files with `updated:` date older than 90 days in active directories (not archive)

**Severity:** MEDIUM for stale drafts. LOW for old-but-stable files. Context matters — a knowledge object about a platform quirk may be old but still valid.

#### Category 7: Template Compliance (full only)

Spot-check a sample of files against their governing templates:

- Pick 2-3 personas: check against `workspace/templates/persona/persona-template.md`
- Pick 2-3 skills: check against `workspace/templates/skill/skill-template.md`
- Check if required sections from the template are present in the actual file

**Severity:** MEDIUM for missing required sections. LOW for structural variations that don't affect functionality.

### 4. Compile report

Organize findings into the output structure. Include:

- **Inventory** — count of files checked per category
- **Findings** — grouped by category, sorted by severity within each category
- **Summary** — total findings by severity, overall workspace health assessment

If quick mode: note which categories were skipped and suggest running `full` if the quick results warrant it.

### 5. Emit completion signal

Log a POSITIVE_SIGNAL event to the workspace learnings file (if maintained):
- `worked_as_designed` — if the audit ran cleanly and produced a structured report
- `caught_real_issue` — if any HIGH findings were surfaced
- `first_use` — on first invocation

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections:**

- `## Inventory` — files and links checked, by category
- `## Findings` — categorized, with severity, evidence, and recommended fix per finding
- `## Summary` — total counts by severity, overall assessment

**Status mapping:**

- `DONE` — all requested categories checked, report produced
- `DONE_WITH_NOTES` — report produced but some categories had limited coverage (e.g., no personas to check in category 7)
- `BLOCKED` — workspace root unreadable or manifest missing
- `NEEDS_CONTEXT` — n/a (the workspace itself is the input)

---

## Event Emission

This skill emits POSITIVE_SIGNAL events to the workspace's learnings log on completion:

- **`worked_as_designed`** — audit completed with structured output
- **`caught_real_issue`** — at least one HIGH finding surfaced
- **`first_use`** — first invocation of this skill

---

## Authority Boundaries

- Can read any file in the workspace
- Cannot write to, modify, rename, delete, or archive any file
- Cannot create tasks in external systems
- Findings are recommendations — the user decides what to fix

---

## Activation

- **Hotwords:** `oa review`, `workspace review`, `full audit`, `system check`
- **Explicit invocation:** `Run the oa-review skill` or `oa review full`
- **Recommended cadence:** Monthly, or after any significant workspace change (new skills, governance updates, structural reorganization)

---

## BAD vs GOOD

| BAD | GOOD |
|-----|------|
| "Your workspace looks great!" (after a 2-minute scan) | "Quick mode: 47 files checked, 3 findings (1 HIGH: broken link in retro.md line 42 → ../templates/strategy/missing.md)." |
| Reports a finding without evidence | "Category 3, HIGH: `workspace/skills/capture.md` — `function:` field missing. Expected one of: gate, steward, workflow, utility." |
| Silently skips a category that had no files to check | "Category 7: 0 personas found to check. Skipped. If you have personas, check they're under `workspace/personas/`." |

---

## Resources

- `workspace/templates/strategy/adversarial-critique.md` — the reasoning strategy this skill composes
- `workspace/templates/output-contract/base.md` — the output envelope
- `workspace/governance/skill-conventions.md` — skill standards this audit validates against
- `workspace/governance/pipeline.md` — pipeline contract this audit checks for consistency
- `.atlas-manifest.yml` — manifest this audit validates against
- `docs/reference/reliability-tiers.md` — context for why structural enforcement matters
- `workspace/skills/review-context.md` — the lighter weekly drift check (complementary, not competing)

---

*Created from `workspace/templates/skill/skill-template.md` (Open Atlas v1.2).*

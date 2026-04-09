# Skill Starter Kit — VERIFY

Post-creation checks for skills produced by `SETUP.md`. Run this against any new skill before declaring the kit complete. Each check is yes/no — if any check fails, fix it before shipping the skill.

This file is also useful as a standalone audit when you suspect an existing skill is incomplete.

---

## How to Run

Point your AI at the new skill file and ask it to walk through this checklist. Each item should resolve to PASS or FAIL. Any FAIL is a blocker — fix it.

Or run it manually: open the skill, scroll through, check each box.

---

## Checks

### Required structure

- [ ] **All required sections present.** The skill has: Purpose, What This Skill Does, What This Skill Does NOT Do, Inputs, Procedure, Output Contract, Completion Status Protocol, Activation, Authority Boundaries.
- [ ] **No TODO or placeholder text.** Every bracketed section from the template has been filled in. No `[fill this in]`, no `[TBD]`, no leftover comment blocks.
- [ ] **Procedure has numbered steps.** Not bullet points, not paragraphs — numbered steps that the AI can follow mechanically.

### Frontmatter

- [ ] **Frontmatter is valid YAML.** Opens with `---`, closes with `---`, parses cleanly.
- [ ] **`atlas_tier: user`** is set. NOT `framework`. This is critical — framework tier means future Atlas upgrades will overwrite the skill; user tier means Atlas leaves it alone.
- [ ] **`function:`** is set to one of: `gate`, `steward`, `workflow`, `utility`. NOT `TBD`. NOT empty.
- [ ] **`hotwords:`** is a non-empty list. At least one trigger phrase.
- [ ] **`title:`** field is present and matches the skill name.
- [ ] **`doc_type: skill`** is set.
- [ ] **`status: draft`** initially (the human can promote later).
- [ ] **`created:`** and **`updated:`** dates are present and valid (YYYY-MM-DD format).
- [ ] **`tags:`** field includes at least `[skill]`.

### Function-specific checks

#### If function is `gate`

- [ ] **NO-HEDGING contract section is present and not empty.** It must declare the forced verdict vocabulary (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED) and the PROOF requirement (every finding cites specific evidence).
- [ ] **Anti-skip clause is present.** The skill explicitly says it cannot silently bypass its own checks — SKIPPED with reason, never silent PASS.
- [ ] **BAD vs GOOD section has at least one example pair.** Gate skills need to demonstrate what hedged-vs-clean output looks like.
- [ ] **Event Emission section declares at least one POSITIVE_SIGNAL qualifier.** Gates emit signal — silent gates produce no learning.

#### If function is `steward`

- [ ] **Event Emission section is present.** Stewards emit POSITIVE_SIGNAL on every run.
- [ ] **At least one POSITIVE_SIGNAL qualifier is declared.** Common ones for stewards: `worked_as_designed` on clean run, `caught_real_issue` on drift, `revealed_gap` on structural finding.
- [ ] **Cadence is documented.** Stewards run on a cadence (weekly, after each task, before each commit). The skill should say what cadence is expected.

#### If function is `workflow`

- [ ] **Completion status protocol is fully specified.** All four statuses (DONE / DONE_WITH_NOTES / BLOCKED / NEEDS_CONTEXT) are documented with conditions.
- [ ] **NO-HEDGING and Event Emission sections are deleted** (or explicitly noted as not applicable). Workflows don't require either.

#### If function is `utility`

- [ ] **Skill is genuinely read-only or single-purpose.** Utilities don't write files (or write only trivial output like a log line). If the skill modifies state in any meaningful way, it's not a utility.
- [ ] **NO-HEDGING and Event Emission sections are deleted.** Utilities don't require either.

### Hotword check (the critical one)

- [ ] **Hotwords don't conflict with existing skills.** Run:
  ```bash
  grep -r "hotwords:" workspace/skills/ workspace/templates/skill/
  ```
  If any hotword in the new skill matches a hotword in an existing skill, that's a BLOCK — pick different hotwords.

### Non-responsibilities (the most-skipped section)

- [ ] **`What This Skill Does NOT Do` section exists and is filled in.** Not "TBD," not "I'll figure this out later."
- [ ] **At least 3 specific non-responsibilities** are listed. Each one is concrete enough to be actionable.
- [ ] **At least one explicit boundary** is documented (e.g., "doesn't modify governance files," "doesn't promote drafts to canonical").

### Voice quality

- [ ] **Output contract uses direct language.** No hedging in the output examples ("should produce", "will probably emit") — say what it does ("produces", "emits").
- [ ] **No banned vocabulary.** Quick scan for: "delve," "leverage," "robust," "seamless," "synergy," "It's important to note that..." Skills that use banned words in their description tend to produce them in their output.

### Workspace integration

- [ ] **Skill file is at the correct path:** `workspace/skills/{hotword-or-name}.md`.
- [ ] **BLUEPRINT.md is updated** to register the new skill (or a note is left for the human to do this manually).
- [ ] **Wiring instructions** for the user's AI tool are surfaced. The user knows how to make the hotwords actually fire in their specific tool.

### Sanity check

- [ ] **The skill is genuinely needed.** Is there an existing skill that already covers this scope? If yes, the new skill should either extend the existing one or replace it, not duplicate it.
- [ ] **The skill has a clear "first use case."** The user can articulate one specific situation where they will invoke this skill within the next week.
- [ ] **The procedure is mechanical.** A reader (human or AI) following the numbered steps should produce the same output every time. If the procedure depends on undocumented judgment, it's not a skill yet — it's a draft.

---

## What to Do If Checks Fail

| Failure | Fix |
|---|---|
| Missing section | Re-run the relevant SETUP.md interview step |
| `atlas_tier: framework` instead of `user` | Edit the frontmatter. Critical |
| `function:` empty or TBD | Pick one of: gate / steward / workflow / utility. Default to workflow if unsure |
| Hotword collision | Pick different hotwords or scope the skill more narrowly |
| Non-responsibilities empty or vague | Push for at least 3 specific items |
| Gate skill missing NO-HEDGING | Add the section. If the user doesn't want forced verdicts, the skill is not a gate — change function to workflow |
| BLUEPRINT not updated | Update it (or surface the gap) |

---

## Output Format

When VERIFY runs, it should produce one of three results:

- **PASS** — all checks pass, skill is ready to ship
- **PASS-WITH-NOTES** — all required checks pass, but at least one soft check (sanity, integration) failed and the user should know
- **BLOCK** — at least one required check failed, skill cannot ship until fixed

Per the NO-HEDGING discipline in `docs/reference/anti-patterns.md`: do not say "looks mostly fine" or "probably good." Pick one of the three verdicts, cite the specific check(s) that drove it.

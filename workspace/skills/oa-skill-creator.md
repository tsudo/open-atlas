---
atlas_tier: framework
title: "oa-skill-creator"
doc_type: skill
status: reviewed
created: 2026-04-13
updated: 2026-04-13
owner: open-atlas
tags: [skill, creation, guided, governance]
function: workflow
hotwords: [create a skill, new skill, build a skill, skill creator]
freedom_level: medium
---

# Skill: oa-skill-creator

**Function:** workflow
**Freedom level:** medium
**Composes strategy:** [`chain-of-thought`](../templates/strategy/chain-of-thought.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `create a skill`, `new skill`, `build a skill`, `skill creator`

---

## Purpose

Walk a user through creating a new skill from scratch. Guides naming, purpose definition, function classification, freedom level selection, hotword assignment, procedure drafting, approval boundary identification, and workspace registration. Produces a ready-to-use skill file that follows workspace conventions.

---

## What This Skill Does

- Loads the skill template and skill conventions as governing references
- Asks structured questions to fill in each section of the template
- Validates hotword conflicts against existing skills
- Classifies function type and freedom level with the user
- Identifies approval boundaries based on the skill's side effects
- Writes the completed skill file to `workspace/skills/`
- Updates `workspace/BLUEPRINT.md` to register the new skill

---

## What This Skill Does NOT Do

- Create skills in bulk — one skill per invocation
- Modify existing skills — use this for new skills only
- Decide function classification or freedom level without user input
- Write to any location outside `workspace/skills/` and `workspace/BLUEPRINT.md`
- Create personas, templates, or other artifact types

---

## Inputs

- **Required:** A description of what the skill should do (can be rough — the procedure refines it)
- **Optional:** A preferred name, known hotwords, or a target function type

---

## Procedure

### 1. Load governing references

Read these files silently (do not output their contents):
- `workspace/templates/skill/skill-template.md` — the template to fill
- `workspace/governance/skill-conventions.md` — naming, frontmatter, function types, freedom levels
- `docs/reference/skill-functions.md` — full reference on function classes

### 2. Name and purpose

Ask the user:
- "What should this skill do? Describe it in 2-3 sentences."
- "What should we call it? Suggest a name if you have one, or I'll propose one."

Apply naming conventions from skill-conventions.md: lowercase, hyphen-separated, no generic names. If the user's name conflicts with an existing skill, say so and suggest alternatives.

Present: the proposed name, a refined 2-3 sentence purpose statement. Confirm before proceeding.

### 3. Function classification

Present the four function types with one-line descriptions:

| Function | When to use |
|---|---|
| `gate` | The skill must produce a forced yes/no verdict (PASS/BLOCK) |
| `steward` | The skill periodically checks the health of a surface |
| `workflow` | The skill runs a multi-step process from input to output |
| `utility` | The skill is a lightweight read-only helper |

Ask: "Which function fits? If you're not sure, `workflow` is the safe default."

Confirm the choice.

### 4. Freedom level

Based on the function choice, state the default freedom level:
- `gate`/`steward` → default `low`
- `workflow` → default `medium`
- `utility` → default `high`

Ask: "The default for [function] is [level]. Does that fit, or should we override? Here's what the levels mean:"

| Level | Meaning |
|---|---|
| `high` | Multiple valid approaches, flexible guidance |
| `medium` | Preferred pattern exists, some variation OK |
| `low` | Specific sequence, minimal deviation |

Confirm the choice.

### 5. Hotwords

Ask: "What trigger phrases should invoke this skill? List 2-4 phrases a user would naturally say."

Check for conflicts:
```bash
grep -r "hotwords:" workspace/skills/
```

If any proposed hotword conflicts with an existing skill, flag it and ask the user to pick a different phrase.

### 6. Procedure drafting

Ask: "Walk me through the steps this skill should follow. I'll structure them into a numbered procedure."

For each step the user describes:
- Number it
- Make it concrete and mechanical (per skill-conventions: "anyone or any AI should be able to follow these steps")
- Ask clarifying questions if a step is vague

Present the full procedure for review. Revise until the user approves.

### 7. Approval boundaries

Ask: "Does this skill write files, create tasks, send messages, or modify anything outside the conversation? If yes, where should it pause for your confirmation?"

If the skill is read-only: note that no approval boundaries are needed.

If the skill has side effects: propose boundaries based on the procedure. Typical patterns:
- Before any file writes
- Before external mutations (API calls, task creation)
- After presenting a plan but before executing it

### 8. Remaining sections

Fill in the remaining template sections based on the conversation:
- **What This Skill Does** — 3-7 bullet points from the procedure
- **What This Skill Does NOT Do** — ask: "What should this skill explicitly *not* do?"
- **Inputs** — required and optional, from the procedure discussion
- **Output Contract** — status mapping (DONE, DONE_WITH_NOTES, BLOCKED, NEEDS_CONTEXT)
- **Authority Boundaries** — what the skill can and cannot do without approval
- **Activation** — hotwords from step 5

For gate skills: add the NO-HEDGING Contract section.
For gate/steward skills: add the Event Emission section.

### 9. Assemble and write

Compose the full skill file using the template structure. Set frontmatter:
- `atlas_tier: user` (user-created skills are always user-tier)
- `status: draft` (new skills start as drafts)
- `owner:` — ask the user's name if not known
- `created:` and `updated:` — today's date

Present the complete skill file for final review. Wait for approval.

### 10. Write and register

After approval:
1. Write the skill file to `workspace/skills/{name}.md`
2. Update `workspace/BLUEPRINT.md` to list the new skill
3. Report both writes

### 11. Forward-test

Ask: "One final check — what happens if your AI interprets these instructions in the most permissive way possible? Would that produce bad outcomes?"

If yes: suggest tightening the freedom level or adding approval boundaries. Offer to revise.

If no: skill creation is complete.

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections:**

- `## Skill Summary` — name, function, freedom level, hotwords
- `## Files Written` — paths of created/modified files

**Status mapping:**

- `DONE` — skill file written, BLUEPRINT updated, forward-test passed
- `DONE_WITH_NOTES` — skill file written but user deferred forward-test or BLUEPRINT update
- `BLOCKED` — couldn't write to `workspace/skills/` (permission issue, name conflict unresolved)
- `NEEDS_CONTEXT` — user's description too vague to start; need more input

---

## Approval Boundaries

- **Before writing the skill file.** The complete file must be presented and approved before writing to disk.
- **Before updating BLUEPRINT.md.** Confirm the registration entry before modifying the blueprint.

---

## Authority Boundaries

- Can read any file in the workspace for context and conflict checking
- Can write to `workspace/skills/` (one new file)
- Can update `workspace/BLUEPRINT.md` (add one entry)
- Cannot modify existing skills, templates, governance, or any other files
- Cannot create personas, knowledge objects, or other artifact types

---

## BAD vs GOOD

| BAD | GOOD |
|-----|------|
| Writes the skill file without showing it first | "Here's the complete skill. Review it before I write?" |
| Picks `gate` because the skill sounds important | "This skill produces recommendations, not forced verdicts — that's `workflow`, not `gate`." |
| Skips hotword conflict check | "The hotword 'review' conflicts with `review-context`. Try 'code review' or 'pr review' instead." |

---

## Resources

- `workspace/templates/skill/skill-template.md` — the governing template
- `workspace/governance/skill-conventions.md` — naming, frontmatter, function types, freedom levels
- `docs/reference/skill-functions.md` — full reference on function classes and freedom level
- `docs/reference/output-contracts.md` — the output contract discipline
- `workspace/BLUEPRINT.md` — the workspace map where skills are registered

---

*Created from `workspace/templates/skill/skill-template.md` (Open Atlas v1.2).*

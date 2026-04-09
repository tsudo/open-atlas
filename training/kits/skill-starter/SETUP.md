---
atlas_tier: framework
title: Skill Starter Kit — SETUP
doc_type: kit-setup
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [kit, skill, setup, ai-guided]
---

# Skill Starter Kit — SETUP

> **What this kit does:** walks you through creating a working skill for your workspace via an AI-guided interview. By the end you'll have a skill file in `workspace/skills/`, properly tagged as your content (`atlas_tier: user`), with a function classification, required frontmatter, a procedure, an output contract, and (for gate skills) a NO-HEDGING contract — all validated against `VERIFY.md`.
>
> **What this kit doesn't do:** make architectural decisions about your workspace, write the skill's content for you (you provide the substance, the kit provides the structure), or wire the skill into your AI tool as a slash command (that's a wiring step you do once, separately — see "Wiring" at the bottom).
>
> **Who this is for:** anyone who wants to create their first skill, or someone creating their fifth skill who wants to make sure they're picking the right function classification.

---

## Before You Start

Confirm:

- You have read `workspace/templates/skill/skill-template.md` at least once (10 minutes — gives you the shape of what you're producing, including the function classification options)
- You have read `workspace/governance/skill-conventions.md` (5 minutes — the one-pager on how skills are organized)
- You have a clear idea of *what workflow* the skill is automating. The best skills come from a real recurring task you've done manually three times. If you've never done it manually, the skill won't have the right shape.
- You have a name in mind. Convention: `{hotword-or-name}.md` lowercase with hyphens (e.g., `capture.md`, `publish-check.md`).

---

## How to Use This Kit

Paste this entire SETUP.md file into an AI tool that can read your workspace and write files (Claude Code, Cursor, Codex, or similar). The AI will run through the interview steps below and produce a working skill file at the end.

If you prefer a single shorter prompt instead of this guided kit, see `training/prompts/create-skill.prompt.md` — it's the lighter version of this same flow.

---

## Interview Steps

The AI should walk through these steps with the user. Don't skip steps; each one feeds into a required section of the final skill.

### Step 1 — What workflow are you automating?

Ask: *"Describe the workflow this skill will run. Be concrete — 'helps with publishing' is too vague; 'before I share a doc externally, scan it for secrets, PII, absolute paths, and internal references — block if any are found' is right. What's the manual process you do today that this skill should automate?"*

If the user can't describe the manual process, the skill isn't ready. Push them to do it manually 1-2 times first, then come back. Skills that automate a process the user has never done end up wrong-shaped.

Capture the answer. This becomes the **Purpose** section.

### Step 2 — What function class is this?

Explain the four function types with one-sentence definitions:

- **gate** — produces a forced verdict (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED). Blocks downstream work on findings. Requires NO-HEDGING discipline. Use for: pre-publish checks, security reviews, code reviews, quality gates.
- **steward** — owns ongoing care of a surface. Periodic, not on-demand. Use for: workspace audits, drift detection, weekly reviews.
- **workflow** — drives a multi-step process from input to output. The most common skill type. Use for: capture, transform, route, generate.
- **utility** — read-only or single-purpose helper. No NO-HEDGING required. Use for: lookups, listings, formatting.

Ask: *"Which of these four matches what your skill does? If you're not sure, default to `workflow` — most skills are workflows. `gate` is specifically for skills that produce forced verdicts; if your skill doesn't, it's not a gate."*

Capture the function. **This is required** — skills without a function are incomplete.

### Step 3 — What hotwords should trigger it?

Ask: *"What phrases should invoke this skill? Pick 1-3 short, memorable hotwords. They should be unique in your workspace — if another skill already uses one of your phrases, pick different ones."*

Run a hotword conflict check:

```bash
grep -r "hotwords:" workspace/skills/ workspace/templates/skill/
```

If there's a collision, push the user to pick different hotwords. Don't ship a skill with a hotword conflict — the AI tool won't know which skill to fire.

### Step 4 — What inputs does it take?

Ask: *"What does the skill need to do its work? File paths, raw text, structured data, tool outputs? List required inputs and any optional inputs with defaults."*

Capture the answer. This becomes the **Inputs** section.

### Step 5 — What outputs does it emit?

Ask: *"What does the skill produce? A file written? A message? A structured report? A verdict?"*

For workflow skills: outputs are typically files or transformed content. For gate skills: outputs are forced verdicts plus findings. For steward skills: outputs are health reports plus POSITIVE_SIGNAL events. For utility skills: outputs are typically read-only data.

Capture the answer. This becomes the **Output Contract** section.

### Step 6 — What does it explicitly NOT do?

Ask: *"List 3-5 things this skill will explicitly NOT do. These are the boundaries that prevent scope creep. Be concrete — 'doesn't make decisions for you' is good; 'isn't perfect' is filler."*

The non-responsibilities section is REQUIRED. Push back on vague items.

### Step 7 — NO-HEDGING discipline (gate skills only)

If function = `gate`, walk through the NO-HEDGING contract:

*"This is a gate skill. Gate skills produce forced verdicts from a fixed set: PASS / PASS-WITH-NOTES / BLOCK / SKIPPED. Hedged language ('looks fine', 'probably good', 'seems okay') is not allowed. Every finding must cite specific evidence (file:line, exact phrase, precise location). The skill cannot silently skip its own checks — if a check can't run, it emits SKIPPED with the reason, never PASS."*

Confirm the user agrees. If they want a softer discipline, the skill should not be a `gate` — it's probably a `workflow` instead.

If function != `gate`, skip this step and delete the NO-HEDGING section from the generated skill file.

### Step 8 — Event emission (gate and steward only)

If function = `gate` or `steward`, walk through POSITIVE_SIGNAL emission:

*"Gate and steward skills emit POSITIVE_SIGNAL events to your workspace's learnings log. The qualifiers are: `worked_as_designed`, `caught_real_issue`, `pass_with_notes_resolved`, `first_use`, `revealed_gap`, `surprising_outcome`. Which of these will your skill emit, and on what triggers?"*

For gate skills: `caught_real_issue` on confirmed BLOCK findings, `worked_as_designed` on clean PASS, `first_use` on first invocation. For steward skills: `worked_as_designed` on clean run, `caught_real_issue` on drift detected, `revealed_gap` if the audit surfaces a structural gap.

If function = `workflow` or `utility`, skip this step and delete the Event Emission section from the generated skill file.

### Step 9 — Generate the skill file

Take the captured answers and produce a complete skill file at `workspace/skills/{name}.md`.

**Critical:** the new file's frontmatter must include `atlas_tier: user` (NOT `framework`). The template ships as framework content; the skills the user creates are theirs.

Use `workspace/templates/skill/skill-template.md` as the structural source. Fill in every bracketed section. Don't leave TODOs or placeholders in the final file.

Delete the NO-HEDGING and Event Emission sections if they don't apply (workflow / utility skills).

### Step 10 — Run VERIFY.md

After writing the file, run the checks in `training/kits/skill-starter/VERIFY.md` against the new skill. If any check fails, fix it before declaring the kit complete.

### Step 11 — Surface the result

Tell the user:

1. The path of the new skill file
2. Its function classification
3. A one-sentence summary of what it does
4. The hotwords that trigger it
5. The wiring instructions below — they need to do this once, separately

---

## Wiring

After the skill is created, the user needs to wire it into their AI tool so it actually fires. This is a one-time step per skill per tool.

| Tool | Wiring |
|---|---|
| **Claude Code** | Add a slash command in `.claude/settings.local.json`, OR reference the skill file in `.claude/CLAUDE.md` with the hotword routing |
| **Cursor** | Reference the skill file in `.cursorrules` with the hotword routing |
| **Codex** | Reference in `AGENTS.md` |
| **Other tools** | Reference the skill file in your tool's system prompt; the procedure section is the runbook your AI follows |

The wiring step is intentionally manual — different tools handle skill activation differently, and there is no portable automation that works across all of them.

---

## What the Kit Should NOT Do

- Skip the function classification "to save time" — it's the most important field, not the most optional one
- Skip the NO-HEDGING contract for gate skills — that defeats the purpose of having a gate
- Invent hotwords that conflict with existing skills — run the grep check before generating
- Set `atlas_tier: framework` on the new skill — that's wrong, it should be `user`
- Modify existing skills — this kit only creates new ones
- Touch any file outside `workspace/skills/` (and the BLUEPRINT.md update)

---

*Open Atlas governs how your AI assistant behaves inside your workspace. If you need to govern how your organization adopts AI more broadly — acceptable use, risk tiers, use-case review, board reporting — that's a different problem at a different layer. See [shelbycanyon.com](https://shelbycanyon.com) for that kind of work.*

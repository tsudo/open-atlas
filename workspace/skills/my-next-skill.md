---
atlas_tier: framework
title: "my-next-skill"
doc_type: skill
status: draft
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [skill, starter, template, scaffold]
function: workflow
hotwords: []
---

# Skill: my-next-skill

> **This is a starter scaffold, not a working skill.** Copy this file, rename it (the filename should match your hotword: e.g., `my-thing.md`), update the frontmatter (especially `title`, `created`, `updated`, `owner`, `function`, and `hotwords`), set `atlas_tier: user`, and fill in the sections below. When you're done, delete this callout block.

**Function:** [pick one: `workflow` | `steward` | `gate` | `utility`]
**Composes strategy:** [pick one if your skill does reasoning work, or omit if it's a pure utility]
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `[your trigger phrase]`, `[alternate phrase]`

---

## Purpose

[2-3 sentences. What this skill is for, in plain language. Frame it from the user's perspective: the friction moment it solves, not the function class it belongs to. Example from `capture.md`: "The 'I have a thought, where does it go?' skill — the first skill most users invoke."]

---

## Procedure

[Numbered steps. Imperative voice, addressing the AI that will execute this skill ("Restate the input," not "The skill restates the input"). Each step does one thing. The procedure should be mechanical enough that an AI can follow it without needing additional judgment.]

1. **[Step 1 — usually: read the input.]** [What to do, what to look for, how to handle ambiguity.]

2. **[Step 2 — if your skill composes a strategy, this is where it happens.]** Compose the [`{strategy-name}`](../templates/strategy/{strategy-name}.md) strategy. Follow its steps.

3. **[Step 3 — the work the skill itself does on top of the strategy.]** [Skill-specific transformation, classification, routing, or output assembly.]

4. **[Step 4 — write the file or produce the artifact, if applicable.]** [Where it goes, what frontmatter it gets, what naming convention to use.]

5. **[Step 5 — return the output in the base contract shape.]** Return the output in the [base contract](../templates/output-contract/base.md) shape with the body sections defined below.

---

## What This Skill Does NOT Do

[At least 3 specific non-responsibilities. This is the most-skipped section and the most important one. A skill without explicit non-responsibilities will drift into doing everything. Force yourself to write 3+ items. Be specific.]

- Does NOT [thing 1 the skill should refuse to do, with a one-sentence reason if non-obvious]
- Does NOT [thing 2]
- Does NOT [thing 3]

---

## Inputs

- **Required:** [What the skill needs to start. Be specific about format and source.]
- **Optional:** [What it can use if provided, with what effect.]
- **Implicit:** [What it loads automatically from the workspace if those files exist. Omit this category if your skill does not auto-load anything.]

---

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

---

## Activation

- **Hotword(s):** `[your trigger phrase]`, `[alternate phrase]`
- **Explicit invocation:** `Read workspace/skills/[your-skill-name].md and run it on: [paste]`
- **Tool wiring:** Claude Code slash command `/[your-skill-name]`; Cursor / Codex / generic — reference this file in the tool's context configuration

---

## Resources

[Files this skill depends on or links to. Strategy template, output contract, related skills, schemas. Keep the list short — only what the skill actually references.]

- [`workspace/templates/strategy/{strategy-name}.md`](../templates/strategy/{strategy-name}.md) — the reasoning strategy this skill composes
- [`workspace/templates/output-contract/base.md`](../templates/output-contract/base.md) — the output envelope this skill composes
- [Add any other files the skill depends on]

---

*[One-line summary of what this skill is and what discipline it enforces. Example from `capture.md`: "Workflow skill composing `structured-extraction` + base output contract. The canonical first skill — high-frequency, low-discipline, default-to-drafts."]*

---

## How to use this scaffold

> **Delete this section when you've filled in the file.**

1. **Copy this file** to a new name. The filename should match your hotword (e.g., `my-thing.md` for hotword `my thing`).
2. **Update the frontmatter:** set `atlas_tier: user`, update `title`, `created`, `updated`, `owner`, `function` (one of `workflow / steward / gate / utility`), and `hotwords` (1-3 short trigger phrases).
3. **Pick a function class.** If you're not sure, default to `workflow`. The other three are specializations.
4. **Pick a strategy** if your skill does reasoning work. The five available strategies live in `workspace/templates/strategy/`. Read each one, pick the closest fit, name it in the header. If your skill is a pure utility (no reasoning), omit the strategy reference.
5. **Read a production skill that's close to what you want to build:**
   - Building a workflow skill? Read `capture.md` (simplest) or `extract-knowledge.md` (durability discipline)
   - Building a workflow skill that handles ambiguity? Read `think-it-through.md` (anti-sycophancy gates)
   - Building a steward skill? Read `review-context.md` (cadence framing, drift checks)
   - Building a gate skill? Read `docs/reference/skill-functions.md` for the gate-class discipline (forced verdicts, NO-HEDGING, PROOF, anti-skip). v1.1 does not ship a production gate skill; the gate pattern is documented in the reference.
6. **Fill in each section.** Be specific. Vague skills don't work reliably.
7. **Test the skill.** Invoke it on a real input. Does it produce the output contract exactly? Does it return a completion status? Does it refuse the things it says it refuses?
8. **Delete this "How to use this scaffold" section** and the callout at the top of the file.
9. **If you got stuck**, read `docs/day-2/creating-skills.md` for the deeper guide, or run the starter kit interview at `training/kits/skill-starter/SETUP.md`.

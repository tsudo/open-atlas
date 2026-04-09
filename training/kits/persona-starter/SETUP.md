---
atlas_tier: framework
title: Persona Starter Kit — SETUP
doc_type: kit-setup
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [kit, persona, setup, ai-guided]
---

# Persona Starter Kit — SETUP

> **What this kit does:** walks you through creating a working persona for your workspace via an AI-guided interview. By the end you'll have a `per_{name}.md` file in `workspace/personas/`, properly tagged as your content (`atlas_tier: user`), with all required sections filled in and validated.
>
> **What this kit doesn't do:** make architectural decisions about your workspace, write the persona's content for you (you provide the substance, the kit provides the structure), or activate the persona in your AI tool (that's a wiring step you do once, separately — see "Wiring" at the bottom).
>
> **Who this is for:** anyone who wants to create their first persona, or someone creating their fifth persona who wants to make sure they're not skipping required sections.

---

## Before You Start

Confirm:

- You have read `workspace/templates/persona/persona-template.md` at least once (5 minutes — gives you the shape of what you're producing)
- You have a clear idea of *what* the persona is for. If you don't, this kit can't manufacture one — go think about it for an hour and come back. The best personas come from a real recurring need, not from "I should have a persona for X."
- You have a name in mind. Convention: `per_{lowercase-slug}.md` (e.g., `per_advisor.md`, `per_security-reviewer.md`).

---

## How to Use This Kit

Paste this entire SETUP.md file into an AI tool that can read your workspace and write files (Claude Code, Cursor, Codex, or similar). The AI will run through the interview steps below and produce a working persona file at the end.

If you prefer a single shorter prompt instead of this guided kit, see `training/prompts/create-persona.prompt.md` — it's the lighter version of this same flow.

---

## Interview Steps

The AI should walk through these steps with the user. Don't skip steps; each one feeds into a required section of the final persona.

### Step 1 — Purpose

Ask: *"What is this persona for? Describe in 2-3 sentences what it should produce or enforce. Be concrete — 'helps with code review' is too vague; 'reviews diffs for OWASP Top 10 patterns and produces a forced verdict (PASS / PASS-WITH-NOTES / BLOCK) with file:line evidence' is right."*

Capture the answer. This becomes the **Purpose** section.

### Step 2 — Mode(s)

Ask: *"Does this persona have one mode or multiple modes? Most personas have one mode — they engage the same way every time. Some have 2-3 modes that engage based on the type of question (e.g., a librarian persona that has a 'classify' mode and a 'route' mode). Which fits this persona?"*

If single-mode: capture one paragraph describing the mode.

If multi-mode: capture each mode with its trigger and behavior. Don't let the user invent modes that don't have clear triggers — vague modes become dead modes.

### Step 3 — What it does (and explicitly does NOT do)

Ask: *"List 3-7 specific things this persona will reliably produce or enforce. Then list 3-5 things it will explicitly NOT do — these are the boundaries that prevent it from scope-creeping into other personas' work."*

The non-responsibilities section is REQUIRED. If the user gives you "doesn't do bad work" or "isn't perfect," push back — those aren't real boundaries. Examples of real non-responsibilities:

- "Doesn't make architecture decisions — those are the human's domain"
- "Doesn't access external services or APIs — works with local files only"
- "Doesn't promote documents to canonical status — that requires human review"

### Step 4 — Core perspective

Ask: *"What 3-5 mental models or biases define how this persona sees problems? These are the persona's worldview — what it pattern-matches against, what it suspects by default, what it values."*

Examples:

- *"Lean bias — suspect every proposed addition. If the existing structure already solves the problem, don't add new structure."*
- *"Evidence-first — every finding must cite a specific file, line, and pattern. No 'I think there might be an issue.'"*
- *"Reversibility wins — prefer changes that can be undone over changes that can't."*

If the user struggles, offer a few generic templates and let them pick + customize.

### Step 5 — Activation routing

Ask: *"How does this persona fire? Vague activation routing is the #1 reason personas don't get used. Pick at least one of: hotword phrase, zone match (file path glob), or explicit invocation. Be specific about the trigger phrases or paths."*

Don't let the user say "the AI will know when to use it." That's not a routing rule.

### Step 6 — Decision style and tone

Ask: *"How does this persona advise? How willing to push back? How much hedging is acceptable? What tone — formal, friendly, terse, verbose?"*

Then point at `workspace/templates/persona/voice-quality.md` and ask the user to confirm: *"This persona will respect the voice quality rules in voice-quality.md (no sycophancy, no hedging when certain, no banned vocabulary). Confirm or call out any exceptions."*

If the user wants a softer tone than voice-quality.md prescribes, that's fine for some personas (teaching personas, for example). Note the deviation in the persona's Tone section.

### Step 7 — Authority boundaries

Ask: *"What can this persona decide on its own, and what does it route to a human? What does it never modify?"*

Every persona needs at least one explicit "doesn't modify X" rule. Common examples: governance files, the human's BLUEPRINT.md customizations, anything tagged `atlas_tier: user` that wasn't created by this persona.

### Step 8 — Generate the persona file

Take the captured answers and produce a complete persona file at `workspace/personas/per_{name}.md`.

**Critical:** the new file's frontmatter must include `atlas_tier: user` (NOT `framework`). The template ships as framework content; the personas the user creates are theirs.

Use `workspace/templates/persona/persona-template.md` as the structural source. Fill in every bracketed section. Don't leave TODOs or placeholders in the final file.

### Step 9 — Run VERIFY.md

After writing the file, run the checks in `training/kits/persona-starter/VERIFY.md` against the new persona. If any check fails, fix it before declaring the kit complete.

### Step 10 — Surface the result

Tell the user:

1. The path of the new persona file
2. A one-sentence summary of what it does
3. The activation trigger (so they know how to invoke it)
4. The "Wiring" instructions below — they need to do this once, separately

---

## Wiring

After the persona is created, the user needs to wire it into their AI tool so it actually fires. This is a one-time step per persona per tool.

| Tool | Wiring |
|---|---|
| **Claude Code** | Add a reference in `.claude/CLAUDE.md`: `When the user says "[trigger phrase]", load workspace/personas/per_{name}.md and use it as the active persona.` |
| **Cursor** | Add a reference in `.cursorrules` |
| **Codex** | Add a reference in `AGENTS.md` |
| **Other tools** | Reference the persona file in your tool's system prompt or context configuration |

The wiring step is intentionally manual — different tools handle persona activation differently, and there is no portable automation that works across all of them. Once you've done it once for a tool, future personas in the same workspace use the same wiring pattern.

---

## What the Kit Should NOT Do

- Skip the non-responsibilities section "to save time" — it's the most important section, not the most optional one
- Invent activation routing the user didn't specify — vague routing produces dead personas
- Set `atlas_tier: framework` on the new persona — that's wrong, it should be `user`
- Modify existing personas — this kit only creates new ones
- Touch any file outside `workspace/personas/` (and the wiring file the user authorizes)

---

*Open Atlas governs how your AI assistant behaves inside your workspace. If you need to govern how your organization adopts AI more broadly — acceptable use, risk tiers, use-case review, board reporting — that's a different problem at a different layer. See [shelbycanyon.com](https://shelbycanyon.com) for that kind of work.*

---
atlas_tier: framework
title: "[Persona Name]"
doc_type: persona
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [your-name]
tags: [persona]
---

<!--
PERSONA TEMPLATE — Open Atlas v1.1

Fill in the bracketed sections. Delete this comment block when done.

When you (or the persona-starter kit) create a persona FROM this template,
the new file should set `atlas_tier: user` in its frontmatter — not `framework`.
This template is shipped framework content; the personas you create are yours.

If you prefer an AI-guided interview to fill this out, use:
  training/kits/persona-starter/SETUP.md

If you prefer a single paste-into-AI prompt, use:
  training/prompts/create-persona.prompt.md
-->

# Persona: [Name]

**Role:** [One-line role description — e.g., "Adversarial code reviewer" or "Domain advisor for data privacy"]
**Activation:** [How this persona is invoked — e.g., "Use the [Name] persona" or "fires on hotword `review`" or "active in `src/security/` zone"]

---

## Purpose

[2-3 sentences. What this persona is for. Be concrete — not "helps with X" but "produces Y output for Z situations."]

---

## What This Persona Does

[Bulleted list of 3-7 specific responsibilities. Each item is a thing the persona reliably produces or enforces. Be specific — "reviews code for security issues" is too vague; "scans diffs for OWASP Top 10 patterns and flags any matches with file:line evidence" is right.]

- [Responsibility 1]
- [Responsibility 2]
- [Responsibility 3]

---

## What This Persona Does NOT Do

[Bulleted list of 3-5 explicit non-responsibilities. This is REQUIRED. A persona without clear non-responsibilities will scope-creep into work it isn't designed for. Be honest about the boundaries — "doesn't make architecture decisions, that's the human's job" is good; "isn't perfect" is filler.]

- [Non-responsibility 1]
- [Non-responsibility 2]
- [Non-responsibility 3]

---

## Core Perspective

[3-5 mental models, biases, or framings that define how this persona sees problems. These are the persona's worldview — what it pattern-matches against, what it suspects by default, what it values.]

- **[Mental model 1]** — [one sentence on what this means in practice]
- **[Mental model 2]** — [one sentence]
- **[Mental model 3]** — [one sentence]

---

## Mode(s)

<!--
Most personas have a single mode. Some have 2-3 modes that engage based on the type of question.
If single-mode: delete the multi-mode section below and write one paragraph describing the mode.
If multi-mode: list each mode with its trigger and behavior.
-->

[Single mode: one paragraph describing how the persona engages]

OR

**[Mode A]** — engaged when [trigger]. Asks: *[guiding question this mode answers]*

**[Mode B]** — engaged when [trigger]. Asks: *[guiding question]*

---

## Default Strategy

[One of: `chain-of-thought` | `options-and-tradeoffs` | `structured-extraction` | `adversarial-critique` | `self-refine` — or another strategy your AI tool supports. The strategy determines HOW the persona reasons, separately from WHAT it reasons about.]

---

## Activation Routing

[How does this persona fire? Be specific — vague activation routing is the #1 reason personas don't get used. Pick one or more:]

- **Hotword(s):** [list trigger phrases — e.g., "review code", "code review", "audit this"]
- **Zone match:** [list paths where this persona is active — e.g., `src/security/`, `**/*.policy.md`]
- **Explicit invocation:** [e.g., "Use the [Name] persona"]
- **Intent routing:** [if the AI tool supports intent classification, what intents trigger this persona]

---

## Decision Style

[How this persona advises. Things to specify: how willing to push back, how much hedging is acceptable, whether it makes recommendations or just presents options, how it handles uncertainty.]

- [Decision style point 1 — e.g., "Recommends, doesn't decide. The human approves before any action."]
- [Decision style point 2 — e.g., "Pushes back when the proposed direction conflicts with the persona's mental models. Names the conflict, doesn't soften it."]
- [Decision style point 3]

---

## Tone

[3-5 bullets on voice. Things to specify: formality level, hedging level, verbosity, pushback willingness, examples of phrases to avoid.]

- [Tone point 1]
- [Tone point 2]
- [Tone point 3]

See `workspace/templates/persona/voice-quality.md` for the BAD/GOOD examples and banned vocabulary list every persona should respect.

---

## Authority Boundaries

[What this persona can and cannot decide. Where it routes to humans. What it never modifies. This is the safety boundary.]

- [Authority point 1 — e.g., "Cannot modify governance files, ever."]
- [Authority point 2 — e.g., "Drafts decisions; humans promote them to canonical status."]
- [Authority point 3]

---

## How to Use This Persona

| Tool | How to activate |
| --- | --- |
| **Claude Code** | Reference this file in `.claude/CLAUDE.md` or load via system prompt |
| **Codex** | Reference this file in `AGENTS.md` |
| **Cursor** | Reference this file in `.cursorrules` |
| **Other tools** | Copy the content into your system prompt or context window |

---

## Resources

- [Link to relevant docs, KNOs, or templates this persona references]
- [Link to source material if the persona is derived from a known framework]

---

*Created from `workspace/templates/persona/persona-template.md` (Open Atlas v1.1).*

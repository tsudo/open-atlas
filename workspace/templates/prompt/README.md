---
atlas_tier: framework
title: Prompt Templates — Reference
doc_type: doc
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [prompt-templates, templates, reference]
---

# Prompt Templates

This directory holds the user-facing prompt templates Open Atlas ships. Each one is a structured thinking pattern you can paste into any AI tool to get a more reliable kind of conversation than ad-hoc prompting produces.

---

## What a prompt template is

A prompt template is a markdown file with placeholders (`{{INPUT_NAME}}`) and a procedure section that the AI follows when you invoke it. You either fill in the placeholders manually before pasting, or you tell the AI "use the analysis template" and provide the inputs conversationally — the AI maps your answers to the right fields.

Prompt templates are *user-paste-into-conversation* artifacts. They are not loaded automatically. They are not skills (skills are workflows the AI invokes by hotword; prompt templates are conversations the user starts manually).

---

## What ships in this directory

| File | Use it when you need |
|---|---|
| `analysis.prompt.md` | Structured reasoning, tradeoff evaluation, planning, risk analysis, decomposition |
| `decision.prompt.md` | A recommendation between choices, go/no-go decisions, picking between named alternatives |
| `extraction.prompt.md` | Pulling facts and structure out of raw text, transcripts, or conversations |

These three cover the dominant prompt-driven thinking patterns. They are not exhaustive — you can write your own following the same shape.

---

## Relationship to strategy templates

Open Atlas v1.1 ships a parallel concept in [`workspace/templates/strategy/`](../strategy/) — *reasoning strategy templates* that **skills** compose internally. The two directories cover similar conceptual territory but operate at different layers:

| Prompt templates (this directory) | Strategy templates ([../strategy/](../strategy/)) |
|---|---|
| User-paste-into-conversation | AI-loaded internally during skill execution |
| Standalone artifacts the user invokes | Composable primitives that skills compose |
| Optimized for human readability and conversational invocation | Optimized for AI execution discipline |
| Used for one-off conversations | Used for repeatable skill procedures |

There is real conceptual overlap:

- `analysis.prompt.md` ↔ `chain-of-thought.md` strategy
- `decision.prompt.md` ↔ `options-and-tradeoffs.md` strategy
- `extraction.prompt.md` ↔ `structured-extraction.md` strategy

**Use prompt templates** when you want to start a conversation with structured thinking and you don't want to invoke a skill. **Use strategy templates** when you're building a skill and need to compose reasoning discipline into its procedure. The two are complementary, not redundant.

---

## How to use a prompt template

1. Open the template file (e.g., `analysis.prompt.md`)
2. Fill in the placeholder values, or tell the AI "use the analysis template and ask me for the inputs"
3. Paste the filled template into your conversation
4. The AI follows the procedure section and produces the structured output

You can also reference a template by path during a conversation: *"Run the procedure in `workspace/templates/prompt/decision.prompt.md` on this question..."*

---

## Where to go next

- **The strategy templates** (the AI-loaded reasoning equivalent) → [`../strategy/`](../strategy/)
- **The skills that compose strategies** → [`../../skills/`](../../skills/)
- **The discipline behind prompt templates and strategies** → [`docs/reference/skill-functions.md`](../../../docs/reference/skill-functions.md)

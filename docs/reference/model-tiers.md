---
title: Model Tiers — Multi-Model Template Routing
doc_type: reference
status: reviewed
created: 2026-04-01
updated: 2026-04-07
owner: open-atlas
tags: [reference, model-tiers, templates, multi-model, routing]
atlas_tier: framework
---

# Model Tiers — Multi-Model Template Routing

When your workspace supports multiple AI models, templates shouldn't hardcode which model to use. Instead, they declare a **tier** — an intent-level preference that the tool resolves to a specific model.

---

## The Three Tiers

| Tier | Intent | Use for |
|------|--------|---------|
| `fast` | Quick, low-cost operations | Extraction, formatting, classification, lookup, simple Q&A |
| `balanced` | Most everyday work | Drafting, code generation, summarization, data analysis |
| `reasoning` | Deep thinking, high stakes | Complex analysis, code review, architecture decisions, adversarial critique |

---

## How It Works

Add `Model-tier:` to your template header:

```markdown
# Template: Analysis
Version: 1.0
Strategy: options-and-tradeoffs
Model-tier: reasoning
```

Your AI tool reads the tier and routes to the best available model in that class. If you only have one model configured, the tier is ignored — everything runs on what you have.

---

## Resolution Examples

| Tier | Claude Code | Codex | Cursor | Generic |
|------|------------|-------|--------|---------|
| `fast` | Haiku | mini | fastest available | default model |
| `balanced` | Sonnet | standard | default | default model |
| `reasoning` | Opus | reasoning | most capable | default model |

The mapping is configured per-tool, not per-template. Templates stay model-agnostic.

---

## Rules

- **Default is `balanced`** — if a template doesn't declare a tier, use your standard model
- **Users can override** — "run this analysis on the fast tier" skips the template's preference
- **Skills inherit from templates** — a skill that invokes a `reasoning`-tier template routes to that tier unless it overrides
- **Personas don't set tiers** — tiers are task-level decisions, not identity-level. The template or skill decides, not the persona

---

## When NOT to Use Tiers

- If you only use one model, don't bother — tiers add no value
- If cost isn't a concern, run everything on `reasoning` — the tiers exist to manage cost/quality tradeoffs
- Don't micro-optimize tier assignment — the difference between `balanced` and `reasoning` matters for complex tasks, not for "should this bullet list use Opus?"

---

## Relationship to Strategies

Tiers and strategies are orthogonal:

- **Strategy** = *how* to reason (chain-of-thought, self-refine, adversarial critique)
- **Tier** = *which model* to reason with (fast, balanced, reasoning)

A `fast` model with `chain-of-thought` strategy is valid. A `reasoning` model with `structured-extraction` is valid. They compose independently.

---

*Inspired by [Fabric's](https://github.com/danielmiessler/fabric) per-pattern model mapping, generalized for any AI tool.*

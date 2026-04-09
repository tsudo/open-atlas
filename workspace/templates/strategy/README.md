---
atlas_tier: framework
title: Reasoning Strategy Templates — Reference
doc_type: doc
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [strategy, templates, composability, reference]
---

# Reasoning Strategy Templates

This directory holds the reasoning strategy templates skills compose. If you've read [`workspace/templates/output-contract/`](../output-contract/) and the base output contract, this is the same architectural pattern applied to a different problem: instead of reinventing what the output *looks like*, skills compose output contracts. Instead of reinventing how to *reason*, skills compose strategies.

---

## What a strategy template is

A strategy template is a written, tested, reusable pattern for a specific kind of reasoning. It tells the AI *how to think* about a problem of a particular shape — not *what to think*. The template enforces discipline (numbered steps, classification rules, output expectations) while leaving the content up to the skill that composes it.

Strategy templates exist because the same reasoning patterns recur across different skills. The decision-help skill needs `options-and-tradeoffs`. The workspace audit skill needs `adversarial-critique`. The capture skill needs `structured-extraction`. Without templates, every skill reinvents the discipline. With templates, reasoning quality improves in one place and propagates to every skill that composes it.

---

## What ships in this directory

Open Atlas v1.1 ships **five strategy templates**, each a peer to the others:

| File | Strategy | When to use |
|---|---|---|
| `adversarial-critique.md` | Find what's wrong | Reviews, audits, quality gates, drift checks |
| `chain-of-thought.md` | Step-by-step reasoning | Complex multi-step analysis where intermediate steps matter |
| `options-and-tradeoffs.md` | Decision support | Multiple viable approaches need comparison |
| `self-refine.md` | Iterative drafting | Writing, content creation, anything that improves with revision |
| `structured-extraction.md` | Pull structure from unstructured input | Notes, transcripts, brain dumps, captured content |

These five cover the dominant reasoning shapes for the kind of work Open Atlas was designed around. They are not exhaustive — v1.2 may add more (a `compare-two-things` template is on the v1.2 candidate list). But the five are enough to compose the four production skills v1.1 ships.

---

## How skills compose a strategy

A skill's `## Procedure` section names the strategy it composes and walks its steps. The pattern:

```markdown
## Procedure

1. **Read the input.** ...

2. **Compose the [`structured-extraction`](../templates/strategy/structured-extraction.md) strategy.** Follow its steps:
   - Identify what must be extracted
   - Classify items as confirmed / inferred / unknown
   - ...

3. **[Skill-specific work on top of the strategy output.]** ...
```

The strategy handles the universal reasoning discipline. The skill defines only what's *unique* to its problem on top of that discipline.

When the AI executes a skill that composes a strategy, it loads both files: the skill (for the procedure and output contract) and the strategy (for the reasoning steps embedded inside the procedure).

---

## Why composable strategies (vs. inline-defined reasoning)

The same reasoning that justifies composable output contracts:

| Inline-defined reasoning (the alternative) | Composable strategies (the choice) |
|---|---|
| Each skill reinvents the discipline | Each skill defers reasoning discipline to a tested template |
| Four skills doing decision-help = four slightly-different decision frameworks | Four skills composing `options-and-tradeoffs` = one consistent decision framework |
| Improving the discipline means editing every skill | Improving the discipline means editing one template |
| Skills get long because they re-explain the reasoning every time | Skills stay short — they reference the strategy and add only what's unique |

The trade-off: skills that need a genuinely-different reasoning shape can't compose any of the five. That's fine — they can write their procedure inline. But before doing that, ask whether one of the five almost fits, and whether the skill could compose it with a minor adaptation. The five strategies are general enough to cover most reasoning needs.

---

## Relationship to prompt templates

Open Atlas v1.1 ships a parallel concept in [`workspace/templates/prompt/`](../prompt/) — *user-facing prompt templates* the user pastes into a conversation manually. The two directories cover similar conceptual territory but operate at different layers:

| Strategy templates (this directory) | Prompt templates ([../prompt/](../prompt/)) |
|---|---|
| AI-loaded internally during skill execution | User-paste-into-conversation |
| Composable primitives that skills compose | Standalone artifacts the user invokes |
| Optimized for AI execution discipline | Optimized for human readability and conversational invocation |
| Used for repeatable skill procedures | Used for one-off conversations |

There is real conceptual overlap:

- `chain-of-thought.md` ↔ `analysis.prompt.md`
- `options-and-tradeoffs.md` ↔ `decision.prompt.md`
- `structured-extraction.md` ↔ `extraction.prompt.md`

**Use strategy templates** when you're building a skill and need to compose reasoning discipline into its procedure. **Use prompt templates** when you want to start a conversation with structured thinking and you don't want to invoke a skill. The two are complementary, not redundant.

---

## What a strategy template is NOT

- **Not a skill.** A skill is a workflow with inputs, procedure, output contract, and completion status. A strategy is a reasoning pattern with no I/O contract — it tells you *how to think*, not what to produce.
- **Not a prompt.** A prompt is a single instance of asking the AI to do something. A strategy template is a reusable pattern that gets composed into many skills.
- **Not the only way to encode reasoning discipline.** A skill can encode reasoning inline if no strategy fits. The templates are a *default*, not a requirement.
- **Not exhaustive.** Five templates cover the dominant patterns in v1.1. More may be added in future versions.

---

## Where to go next

- **See strategies in use** → any production skill in [`workspace/skills/`](../../skills/) — look at the procedure section
- **Read a specific strategy** → start with [`options-and-tradeoffs.md`](options-and-tradeoffs.md), the most-used one
- **The parallel concept for output formatting** → [`workspace/templates/output-contract/`](../output-contract/)
- **Understand the discipline behind strategies** → [`docs/reference/skill-functions.md`](../../../docs/reference/skill-functions.md)

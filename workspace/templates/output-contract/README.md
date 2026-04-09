---
atlas_tier: framework
title: Output Contracts — Reference
doc_type: doc
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [output-contracts, templates, composability, reference]
---

# Output Contracts

This directory holds the output contract templates skills compose. If you've read [`workspace/templates/strategy/`](../strategy/) and the four reasoning strategies, this is the same architectural pattern applied to a different problem: instead of reinventing how to *reason*, skills compose strategies. Instead of reinventing what their *output looks like*, skills compose output contracts.

---

## What an output contract is

An output contract is a written promise about *what a skill will produce*. It defines the shape, the required sections, the status vocabulary, and the rules for what goes where. The contract is the difference between "the AI gave me something useful" and "the AI gave me exactly the thing it said it would give me."

The default failure mode for AI-assisted work is **soft drift**: outputs that are roughly the right shape, in roughly the right format, with roughly the right level of detail. None of those "roughlys" are individually bad, but they compound. After a few sessions you can't tell which outputs to trust and which to re-run. Output contracts eliminate the roughlys.

For deeper background on the discipline behind output contracts (forced verdicts, evidence requirements, completion status), read [`docs/reference/output-contracts.md`](../../../docs/reference/output-contracts.md). This README is about the *templates* that operationalize that discipline.

---

## What ships in this directory

| File | Purpose | Composed by |
|---|---|---|
| `base.md` | The universal output envelope every skill output uses — frontmatter, summary, notes, status footer. | All four production skills + `my-next-skill.md` starter |

v1.1 ships one contract template. v1.2 will likely add specialized variants (a gate-output contract with forced-verdict structure baked in, a steward-output contract with health-report sections baked in). For now, the base contract is general enough to compose any of the four function classes.

---

## How skills compose an output contract

A skill's `## Output Contract` section names the contract it composes and lists the skill-specific sections that go inside the envelope. The pattern:

```markdown
## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Skill-specific sections** (go inside the base envelope's body):

- `## Decision` — the restated decision and its shape
- `## Options` — 2-3 options with gain / give-up / could-go-wrong
- `## Recommended Default` — with confidence and flip-condition
- ...
```

The base contract handles the universal envelope (what frontmatter to set, where the status goes, how to format the notes section). The skill defines only what's *unique* to it. This keeps skill files short and consistent.

When the AI executes a skill that composes the base contract, it:

1. Loads the base contract template to understand the envelope
2. Loads the skill file to understand the body sections
3. Produces output that is structurally a base contract with the skill's body sections filled in

---

## Why composable output contracts (vs. inline-defined contracts)

The same reasoning that justifies composable strategy templates:

| Inline-defined contracts (the alternative) | Composable contracts (the choice) |
|---|---|
| Each skill defines its own output shape from scratch | Each skill defines only what's unique; the envelope is shared |
| Four skills = four slightly-different shapes | Four skills = four predictable variations of the same shape |
| Adding a new skill means deciding the envelope again | Adding a new skill means picking which sections to include |
| Improving the envelope means editing every skill | Improving the envelope means editing one file |
| Users have to re-learn where the status lives in each skill's output | Users learn the envelope once and recognize it everywhere |

The trade-off: skills that need a genuinely-different envelope shape can't compose the base contract. That's fine — they can define their contract inline and accept the consistency cost. The base contract is a *default*, not a *requirement*. If you find yourself fighting it, write your own.

---

## What an output contract is NOT

- **Not a schema enforcer.** The contract is a written promise, not a parser. There is no validation step that rejects a non-conforming output. The discipline lives in the AI following the contract, not in tooling rejecting violations.
- **Not a substitute for the procedure.** A contract describes the *shape* of the output. The procedure describes *how to produce it*. Both are needed.
- **Not the same thing as a strategy.** A strategy template tells the AI *how to reason*. An output contract tells the AI *how to format what it produced*. Skills typically compose one of each.
- **Not a schema.md file.** The schema files in `workspace/templates/schema/` define document types (KNO, frontmatter, etc.). Output contracts define skill outputs. Different layers.

---

## Where to go next

- **Read the base contract** → [`base.md`](base.md)
- **See contracts in use** → any production skill in [`workspace/skills/`](../../skills/) — look at the `## Output Contract` section
- **Understand the discipline** → [`docs/reference/output-contracts.md`](../../../docs/reference/output-contracts.md)
- **The parallel concept for reasoning** → [`workspace/templates/strategy/`](../strategy/)

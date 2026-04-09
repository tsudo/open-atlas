---
atlas_tier: framework
title: "Strategy: Chain-of-Thought"
doc_type: strategy-template
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [strategy, chain-of-thought, reasoning]
---

# Strategy: Chain-of-Thought

**When to use:** Complex reasoning, multi-step analysis, debugging, architectural decisions, any problem where intermediate steps matter.

**When NOT to use:** Simple factual queries, extraction tasks, formatting jobs.

---

## Instruction

Think step by step. For each step:

1. State what you're considering and why it matters
2. Evaluate it against the known constraints
3. Draw a conclusion before moving to the next step

After all steps, synthesize into a final answer. Show your reasoning — the intermediate steps are as valuable as the conclusion.

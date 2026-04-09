---
atlas_tier: framework
title: Skills — Orientation
doc_type: doc
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [skills, orientation, getting-started]
---

# Skills

This directory holds the skills your AI can run inside this workspace. Read this file once. After that, the skill files speak for themselves.

---

## What a skill is

A skill is a defined workflow your AI can execute. It has inputs, a procedure, an output contract, and a completion status. The skill is a written promise: *given this input, follow this procedure, produce this output, end in one of these states.*

Skills exist because AI assistance is most useful when it's *consistent*. The same request shouldn't give you a different shape of answer every time you ask. Skills make the shape of the answer the part you decide once and reuse forever.

You don't need to write a skill for every task. Write a skill when:

- You've done the same multi-step thing more than twice
- You want it done the same way every time
- You're willing to write the procedure down (because that's what the skill is)

For one-off work, just talk to the AI. Skills are for the workflows that recur.

---

## What ships in this directory

Open Atlas v1.1 ships **four production skills and one starter** in `workspace/skills/`. They are not example files — they are the working skills your AI runs when you invoke them.

| File | What it's for | Function class |
|---|---|---|
| `capture.md` | "I have a thought / a paste / a chunk of context — where does it go?" | workflow |
| `think-it-through.md` | "Help me think through this decision without telling me what I want to hear" | workflow |
| `extract-knowledge.md` | "I learned something today — capture it before I lose it" | workflow |
| `review-context.md` | "Is my workspace drifting? Run a health check." | steward |
| `my-next-skill.md` | Starter scaffold — copy, rename, fill in to build your own skill | (template) |

Together, the four production skills form a complete loop: capture in → think things through → extract what's durable → maintain the workspace. A new user who runs all four within their first week has used Open Atlas the way it's meant to be used.

**Read the skills themselves.** They're written to be readable. Studying them is how you'll learn to write your own.

---

## The four function classes

Every skill belongs to exactly one function class. Picking the right one is the most consequential design decision when you build a skill.

| Class | When to use | What makes it different |
|---|---|---|
| **`workflow`** | Multi-step process from input to output. The most common class. | Procedure + output contract. The default when none of the others obviously fits. |
| **`steward`** | Periodic ongoing care of a surface (workspace audits, drift checks, health reviews). | Cadence-based, not on-demand. Read-only. Produces a report you act on. |
| **`gate`** | Forced verdicts at decision points (pre-publish checks, code review checkpoints). | Forced verdict vocabulary (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED). Hedged language is forbidden. Every finding cites evidence. |
| **`utility`** | Read-only helper, lightweight. | Minimal structure. Stays small on purpose. |

If you're not sure which one fits, default to `workflow`. The other three are specializations of the same base shape.

For depth on each class, read [`docs/reference/skill-functions.md`](../../docs/reference/skill-functions.md).

---

## Skills compose reasoning strategies

Open Atlas ships five reasoning strategy templates in [`workspace/templates/strategy/`](../templates/strategy/):

- **`adversarial-critique`** — for reviews, audits, finding-what's-wrong work
- **`chain-of-thought`** — for complex multi-step reasoning where intermediate steps matter
- **`options-and-tradeoffs`** — for decisions with multiple viable approaches
- **`self-refine`** — for drafting and content creation that benefits from iteration
- **`structured-extraction`** — for pulling structure from unstructured input

**Skills compose these strategies rather than reinventing how to reason.** When a skill says "compose `options-and-tradeoffs`," it means the AI loads that strategy template and follows its discipline — which has been refined and tested separately from any individual skill.

This is the load-bearing design choice for v1.1. It keeps skills short, makes them composable, and lets reasoning quality improve in one place (the strategy template) instead of being copy-pasted across every skill that needs it.

When you write your own skill, ask: *which of the five strategies fits the reasoning shape this skill needs?* Compose it. Don't reinvent it.

---

## How to read a production skill as your own learning path

Pick the one closest to what you want to build and read it top-to-bottom.

- **Want to build a workflow skill?** Read `capture.md`. It's the simplest of the four. Pay attention to the procedure section and the output contract.
- **Want to build something that handles ambiguity gracefully?** Read `think-it-through.md`. It composes `options-and-tradeoffs` and shows how to load decision heuristics without requiring the user to have configured anything.
- **Want to build a steward skill?** Read `review-context.md`. Pay attention to the cadence framing and the difference between "what to flag" and "what to decide."
- **Want to write a skill that produces a durable artifact?** Read `extract-knowledge.md`. Pay attention to how it stays useful even when `workspace/knowledge/` is empty.

Then copy `my-next-skill.md`, rename it, fill in the sections, and you'll have a skill in your workspace.

---

## How to invoke a skill

In Claude Code with slash commands wired: type `/capture` (or whichever skill).

Without slash commands wired: tell the AI to read the skill file and run it. Example:

> Read `workspace/skills/capture.md` and run it on this content: [paste your content].

Both work. Slash commands are convenience polish; reading the file by path is the canonical way to invoke any skill in any AI tool that can read files.

For wiring per AI tool, see [`docs/day-2/creating-skills.md`](../../docs/day-2/creating-skills.md#wiring-the-skill-into-your-ai-tool).

---

## When you're ready to build your own

Two paths:

1. **Copy `my-next-skill.md`**, rename it, fill in the sections. Fastest path. Best when you already know what you want.
2. **Run the starter kit interview** at [`training/kits/skill-starter/SETUP.md`](../../training/kits/skill-starter/SETUP.md). Structured. Best when you want guidance on which function class to pick and what each section should contain.

Either way, your new skill should:

- Set `atlas_tier: user` in its frontmatter (so the upgrade tool leaves it alone)
- Pick one of the four function classes
- Compose one of the five strategies if it does any reasoning work
- Have a `What This Skill Does NOT Do` section (this is the most often skipped, and the most important)
- Have an explicit completion status (`DONE / DONE_WITH_NOTES / BLOCKED / NEEDS_CONTEXT`)

When you run into trouble, read [`docs/day-2/creating-skills.md`](../../docs/day-2/creating-skills.md) for the deeper guide.

---

## What this directory is NOT

- **Not a kitchen sink.** Open Atlas ships four production skills on purpose. They cover the lifecycle. Adding more skills here is your job, not Open Atlas's.
- **Not the only place skills can live.** A skill that's specific to one project belongs in that project's directory, not here. This directory is for skills that work across the whole workspace.
- **Not a substitute for thinking.** A skill that runs the wrong procedure consistently is worse than no skill at all. The skills here are starting points; review the ones you use, edit them when they don't fit, retire them when they stop earning their keep.

---

## Where to go next

- **Run your first skill** → start with `capture.md`
- **Understand the function classes in depth** → [`docs/reference/skill-functions.md`](../../docs/reference/skill-functions.md)
- **Understand the output contract discipline** → [`docs/reference/output-contracts.md`](../../docs/reference/output-contracts.md)
- **Build your own skill** → `my-next-skill.md` or [`training/kits/skill-starter/SETUP.md`](../../training/kits/skill-starter/SETUP.md)
- **Learn the broader workspace conventions** → [`workspace/README.md`](../README.md)

---
atlas_tier: framework
title: "think-it-through"
doc_type: skill
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [skill, decision-help, options-and-tradeoffs, anti-sycophancy]
function: workflow
hotwords: [think it through, help me decide, weigh this, work through this]
---

# Skill: think-it-through

**Function:** workflow
**Composes strategy:** [`options-and-tradeoffs`](../templates/strategy/options-and-tradeoffs.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `think it through`, `help me decide`, `weigh this`, `work through this`

---

## Purpose

Help the user think through a decision they're stuck on, without telling them what they want to hear. Produce 2-3 viable options with honest tradeoffs and a recommended default. The skill exists to make AI-assisted decision support *less sycophantic and more useful* than the default conversation.

---

## Procedure

1. **Restate the decision in one plain-language sentence.** If your restatement changes the meaning of what was said, surface that — it's a signal the framing was unclear.

2. **Identify the decision shape:** binary (yes/no), named-alternatives (X or Y), or open-ended ("what should I do about Z"). Shape determines how you generate options.

3. **Load context, gracefully.** Try to read each of these. If a file doesn't exist, note it once and continue:
   - `workspace/context/human-operating-model.md` — how the user decides
   - `workspace/context/beliefs-and-mental-models.md` — decision heuristics they reach for
   - Any files the user explicitly named in their input

   Do NOT search the workspace broadly. Loading the wrong files is worse than loading nothing.

4. **Compose the [`options-and-tradeoffs`](../templates/strategy/options-and-tradeoffs.md) strategy.** Follow its 7 steps:
   - Frame the decision and its success criteria
   - Generate 2-3 viable options (or use the user's options if provided)
   - For each option, state what you gain, what you give up, what could go wrong
   - Recommend a default with confidence (high / medium / low) and explain why
   - Name the conditions under which you'd change the recommendation
   - State whether the decision is reversible

5. **Apply the heuristics in order.** If you loaded `beliefs-and-mental-models.md`, use the user's heuristics. Otherwise use these defaults:
   - **Reversibility test** — favor reversible options when stakes are unclear; lower the stakes in the framing
   - **Leverage test** — favor options that compound over options that are one-shot
   - **"What if this happened?" test** — surface 1-2 plausible failure scenarios for the recommended default and how the user would recover
   - **Pattern-first test** — if the user named an instinct, test it explicitly against the options instead of ignoring it
   - **Cooperation bias** — when stakeholders are involved, favor influence-based options over force-based ones

6. **The "you didn't mention" gate.** Identify 1-2 things the user did NOT mention that would meaningfully change the recommendation. Name them in the output. Ask the user to confirm or correct. This is the first anti-sycophancy gate — it forces you to think about what's missing instead of optimizing for the framing you were given.

7. **The flip-the-question gate.** Run this concrete test before finalizing: *imagine the user had stated the opposite preference. (If they said "I want to use SQLite," imagine they said "I want to use Postgres.") Would your recommendation flip to match the new preference, or stay the same because the underlying reasoning is independent of what they wanted to hear?* **Make this check visible by writing the result to a `Flip-test result:` line inside the `## Recommended Default` section of the output.** If the recommendation would flip, the recommendation is sycophancy — revise the recommendation before returning. If it would stay the same, the recommendation has conviction — keep it. The visibility requirement is the load-bearing piece: an unwritten flip-test is one the AI can rationalize past; a written flip-test is one the user can audit. This is the second anti-sycophancy gate.

8. **Return the output in the [base contract](../templates/output-contract/base.md) shape with the body sections defined below.**

---

## What This Skill Does NOT Do

- Does NOT make the decision for the user. The output is structured thinking-through, not a verdict.
- Does NOT pretend to be neutral when it has a recommendation. Hedging is worse than picking the wrong default — at least a wrong default is testable.
- Does NOT require any prior context files. Works on day 1 with an empty workspace; works *better* with an operating model and beliefs file.
- Does NOT capture the decision as a durable artifact. The user invokes `capture` or `extract-knowledge` separately if they want to save it.
- Does NOT do open-ended brainstorming. If there are no constraints and no decision pending, this is the wrong skill — use general conversation.
- Does NOT modify any files. The output is returned in conversation; persistence is the user's call.
- Does NOT promote a recommendation to "decided" state. That's a human action.

---

## Inputs

- **Required:** A decision the user is trying to make. A sentence or a paragraph. Vagueness is fine — the procedure handles it.
- **Optional:** Constraints, deadlines, budgets, prior commitments, stakeholders.
- **Optional:** A list of options the user has already identified. If provided, work with these instead of generating your own.
- **Implicit:** Anything in `workspace/context/` (operating model, beliefs). Loaded automatically if present.

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections** (in this order):

- `## Decision` — the restated decision in one sentence + its shape (binary / named-alternatives / open-ended)
- `## Context Loaded` — list of files actually read, or "none — running on first principles"
- `## Options` — 2-3 options, each with `Gain`, `Give up`, `Could go wrong` sub-items
- `## Reversibility` — reversible / partially reversible / irreversible, with one sentence on why
- `## Recommended Default` — the named option, confidence (high / medium / low), 2-3 sentence reasoning, an explicit "I'd change this if:" condition, AND a `Flip-test result:` line that explicitly states what the recommendation would be if the user's stated preference were reversed (the visible output of step 7's flip-the-question gate). If `Flip-test result:` shows the recommendation would change, the recommendation is sycophancy and must be revised before the output is returned. If it shows the recommendation would stay the same, the gate passed.
- `## You Didn't Mention` — 1-2 things the user did NOT mention that would change the recommendation if true; ask them to confirm or correct

**Status mapping:**

- `DONE` — produced 2-3 options with full tradeoffs, a recommendation with confidence, the reversibility assessment, and the "you didn't mention" prompt; flip-the-question gate passed
- `DONE_WITH_NOTES` — produced the artifact but with a significant caveat (e.g., context files missing, framing internally contradictory and you picked one interpretation). Notes section explains.
- `BLOCKED` — input is not actually a decision, or the user asked for something this skill is not designed to do
- `NEEDS_CONTEXT` — input is too vague to generate options without guessing; Notes section names the specific clarifying question needed

---

## Activation

- **Hotword(s):** `think it through`, `help me decide`, `weigh this`, `work through this`
- **Explicit invocation:** `Read workspace/skills/think-it-through.md and run it on this decision: [paste]`
- **Tool wiring:** Claude Code slash command `/think-it-through`; Cursor / Codex / generic — reference this file in the tool's context configuration

---

## Resources

- [`workspace/templates/strategy/options-and-tradeoffs.md`](../templates/strategy/options-and-tradeoffs.md) — the reasoning strategy this skill composes
- [`workspace/templates/output-contract/base.md`](../templates/output-contract/base.md) — the output envelope this skill composes
- [`workspace/context/human-operating-model.md`](../context/human-operating-model.md) — the user's operating model (loaded if present)
- [`workspace/skills/capture.md`](capture.md) — invoke separately to save the thinking-through artifact
- [`workspace/skills/extract-knowledge.md`](extract-knowledge.md) — invoke separately if the user realized something durable

---

*Workflow skill composing `options-and-tradeoffs` + base output contract, with anti-sycophancy discipline (reversibility test, "you didn't mention" gate, flip-the-question gate) and graceful context loading.*

---
atlas_tier: framework
title: "Before & After: Same Prompt, Two Workspaces"
doc_type: doc
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [example, before-after, demonstration]
---

# Before & After

The same question, asked of an AI working in two different workspaces. No prompt engineering tricks. Just structure.

---

## The prompt

> I've been thinking about whether to standardize our team on one AI tool or let people pick their own. I keep going back and forth. Help me think this through.

---

## Before — Junk-drawer directory

The AI is working in a directory with a `Documents/` folder, some PDFs, a few half-finished notes, and no conventions.

**What the AI does:**

> That's a great question! Standardizing on one tool has benefits like consistency, easier support, and shared knowledge. On the other hand, letting people choose can boost productivity and morale. Here are some things to consider:
>
> 1. **Cost** — bulk licenses are usually cheaper
> 2. **Training** — easier to train on one tool
> 3. **Integration** — fewer tools means simpler integrations
> 4. **Flexibility** — different roles have different needs
> 5. **Innovation** — diversity of tools surfaces new approaches
>
> Ultimately, it depends on your team's specific situation. You might want to survey your team to understand their preferences. Would you like help drafting a survey?

**What's wrong with this:**

- Generic. The AI has no idea who you are, what your team does, or what you've already tried.
- Hedged. "It depends on your specific situation" is the AI saying *I have nothing.*
- Routes to more work (a survey) instead of helping you decide.
- The output is identical to what every other person who asked this question got.
- Nothing is captured. In a week you won't remember you asked this.

---

## After — Open Atlas workspace

The AI is working in an Open Atlas workspace. There's a `workspace/personas/per_open-atlas.md` file. There's a `workspace/skills/capture.md` skill. There's a `workspace/context/human-operating-model.md` you wrote two weeks ago. There's a `workspace/knowledge/` directory with two prior decisions about tooling.

**What the AI does:**

> I'm activating `per_open-atlas` (the workspace's default advisor persona) to handle this. Two things first:
>
> 1. I see you have a prior decision in `workspace/knowledge/decisions/2026-02-tool-procurement.md` where you committed to "consolidating where consolidation reduces friction, not for its own sake." I'll honor that as a constraint unless you tell me to revisit it.
>
> 2. Your human-operating-model says you decide by generating options and evaluating tradeoffs, then committing decisively. I'll structure this as options, not as a survey.
>
> **Three options:**
>
> **A. Hard standardize on one tool.** Lowest support cost, fastest team learning curve, but you lose the people who are productive *because* of their current tool. Tradeoff: optimize for the median user, accept the cost of losing your top 10%.
>
> **B. Soft standardize — one default, exceptions allowed.** Most of the cost savings of A, most of the flexibility of letting people pick. Tradeoff: requires you to maintain the exception process and the inevitable "why is mine an exception" politics.
>
> **C. Pick a tool *category*, let people pick within it.** E.g., "everyone uses an agentic AI tool, you can pick Claude Code, Cursor, or Codex." Tradeoff: you get tool diversity but a shared mental model — most of the consolidation benefit without the lock-in.
>
> **My recommendation:** Option C. It honors your prior decision (consolidation reduces friction *only* where it actually does), it matches your operating style (decisive commitment to a category, flexible execution within it), and it's the least likely to require revisiting in six months.
>
> **Next move:** I can capture this as a draft decision record at `workspace/drafts/ai-tool-standardization-decision.md` so you can sit with it and come back. Or I can run it through `per_advisor` for an adversarial pass before you commit. Your call.

**What's different:**

- Specific. The AI grounded the answer in two existing files in your workspace.
- Opinionated. Three options, one recommendation, with reasoning.
- No survey. The AI inferred from your prior decision and your operating model that you decide differently.
- Captured. The next move is a draft you can find tomorrow.
- Bounded. The persona's authority stops at "draft" — promotion is your call.

---

## What changed between the two

The model is the same. The prompt is the same. The user is the same.

What changed:

1. **The AI had an operating model to read** (`human-operating-model.md`)
2. **The AI had prior decisions to ground in** (`workspace/knowledge/decisions/`)
3. **The AI had a persona to adopt** (`per_open-atlas.md`)
4. **The AI had a skill to route to** (`capture`)
5. **The AI had a clear authority boundary** (drafts, not finals)

None of those required configuration. They're just files in predictable locations. Open Atlas is the convention that puts them there and tells the AI to read them.

---

## What this is not

This is not a benchmark. It's not a controlled experiment. The "before" output is realistic — it's what an AI gives you in a junk-drawer directory — but a *very* careful prompter could pull better output from the same setup. The point is the *floor*, not the ceiling: in an Open Atlas workspace, even a casual question gets a grounded, opinionated, bounded answer. In a junk-drawer directory, even a careful question can get a hedged, generic one.

The framework isn't doing the thinking. It's giving the AI the context to think with.

---

## Where to go next

- **The walkthrough** — three templates building on each other → [walkthrough.md](walkthrough.md)
- **First session** — how to actually use Open Atlas → [../docs/day-1/first-session.md](../docs/day-1/first-session.md)
- **Why this works** — the design rationale → [../docs/reference/why-this-works.md](../docs/reference/why-this-works.md)

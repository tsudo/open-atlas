# Reliability Tiers

Not all rules enforce equally. A rule in your tool instruction file auto-loads every session but depends on the AI choosing to follow it. A hook fires mechanically every time the triggering event occurs, regardless of what the AI is thinking about. Understanding where your rules sit on the enforcement spectrum tells you which ones will hold under pressure and which ones will silently fail.

---

## The spectrum

| Tier | Name | Mechanism | Reliability |
|---|---|---|---|
| **0** | Conversational | Said in a chat message | Gone next session. Zero persistence. |
| **0.5** | Auto-loaded instructions | Written in your tool's instruction file (CLAUDE.md, AGENTS.md, .cursorrules, or equivalent) | Loaded every session, but AI-interpreted. May be ignored under cognitive load or when competing instructions conflict. |
| **1** | Memory-based | Written in a persistent file the AI loads on demand (learnings file, memory index, context docs) | Available if the AI reads the file. Often it doesn't — especially under time pressure or when the task doesn't obviously relate to the memory topic. |
| **2** | Persona-embedded | Encoded in a persona definition that activates for specific work | Active when the persona is active. Dormant otherwise. A security check baked into your security persona only fires when someone activates that persona. |
| **3** | Skill-based | Encoded in a skill's procedure | Procedural enforcement when the skill is invoked. But someone has to invoke it — if the user skips the skill, the rule doesn't fire. |
| **5** | Hook-based | Mechanically triggered by tool events (PreToolUse, PostToolUse, or equivalent) | Unconditional. Fires every time the event occurs. The AI doesn't choose whether to follow it — the tool runtime enforces it before or after the action. |

### Where is Tier 4?

Intentionally unassigned. The jump from Tier 3 (skill-invoked, procedural) to Tier 5 (hook-triggered, unconditional) reflects a real reliability cliff. Skills depend on a human or AI deciding to invoke them. Hooks don't. There is no intermediate mechanism in current AI tool architectures that bridges this gap — nothing that's "more than procedural but less than unconditional." If a future tool pattern fills that space (automated skill invocation on file events, for example), Tier 4 is reserved for it.

---

## Why the distinction matters

Most workspace rules live at Tier 0.5. They're written in the instruction file, the AI reads them at session start, and... sometimes follows them. Under cognitive load — long sessions, complex multi-step tasks, competing instructions — Tier 0.5 rules are the first to drop. The AI doesn't maliciously ignore them. It just runs out of attention.

This creates a predictable failure pattern by tier band:

- **Tiers 0-0.5** fail under cognitive load. The AI forgets or deprioritizes.
- **Tier 1** fails on retrieval. The AI doesn't look up the memory file, or looks it up but doesn't apply the relevant entry.
- **Tier 2** fails on activation. The right persona wasn't loaded for this task.
- **Tier 3** fails on invocation. The user didn't run the skill, or ran it but skipped to a later step.
- **Tier 5** doesn't fail silently. If the hook breaks, it breaks loudly (errors, blocked actions). You find out immediately.

The higher the tier, the more the enforcement depends on machinery rather than attention. That's the core insight: **rules that depend on memory fail; rules that depend on tooling hold.**

---

## Audit your workspace

Count your rules by tier. Most workspaces look like this:

```
Tier 5:   ██ (2 rules)
Tier 3:   ████ (4 rules)
Tier 2:   ███ (3 rules)
Tier 1:   ████████ (8 rules)
Tier 0.5: ████████████████████ (20 rules)
```

The distribution is bottom-heavy. 28 of 37 rules live at the two least reliable tiers. This isn't wrong — not every rule needs a hook. But the rules that matter most should sit higher.

To run this audit:

1. List every behavioral rule in your tool instruction file (CLAUDE.md or equivalent). Count them. That's your Tier 0.5 total.
2. List every rule in your memory/learnings files. That's Tier 1.
3. Check each persona for embedded behavioral rules. That's Tier 2.
4. Check each skill's procedure for enforcement steps. That's Tier 3.
5. List your hooks. That's Tier 5.

Now ask: which of my Tier 0.5 rules have I seen the AI violate? Those are promotion candidates.

---

## Promotion paths

Not every rule needs to be Tier 5. The goal is to match enforcement level to consequence of failure. A style preference is fine at Tier 0.5. A security check that protects credentials should be Tier 5.

### Tier 0 → 0.5: Write it down

If you keep telling the AI the same thing every session, put it in your instruction file. This is the simplest promotion and the most effective one — it eliminates the most common failure mode (forgetting to mention it).

### Tier 0.5 → 1: Put it in a learnings file

When a rule has context that's too long for the instruction file (the full story of why, the edge cases, the exceptions), move the detail to a learnings file or context document. Keep a one-line pointer in the instruction file. The AI loads the detail when the topic is relevant.

**When not to promote:** If the rule is short and always relevant, Tier 0.5 is better. Tier 1 adds a retrieval step that may not happen.

### Tier 1 → 2: Embed it in a persona

When a rule only matters in a specific domain (security rules during code review, brand rules during content creation), embed it in the relevant persona. The rule activates with the persona and doesn't clutter unrelated work.

**When not to promote:** If the rule matters across domains, a persona is the wrong home — it'll be dormant when it shouldn't be.

### Tier 2 → 3: Encode it in a skill

When a rule needs a multi-step procedure to enforce correctly (not just "remember this" but "check A, then B, then verify C"), encode it as part of a skill's procedure. The skill enforces the full sequence when invoked.

**When not to promote:** If the rule is a simple constraint rather than a procedure, a persona or instruction file is simpler. Skills add maintenance overhead.

### Tier 3 → 5: Implement it as a hook

When a rule must fire unconditionally — regardless of which persona is active, whether a skill was invoked, or what the AI is thinking about — implement it as a hook. Hooks are the only tier that doesn't depend on the AI cooperating.

**When not to promote:** If the rule requires judgment (not just pattern matching), a hook can't enforce it well. Hooks work best for mechanical checks: file path validation, credential leak detection, format enforcement.

---

## Connection to the feedback loop

The [feedback loop](feedback-loop.md) uses POSITIVE_SIGNAL events to track which skills earn their keep. Reliability tiers add a dimension to that signal: a Tier 3 skill that fires correctly is good, but a Tier 5 hook that catches what the skill missed is better evidence. When reviewing your workspace health, check not just *whether* rules fired but *at which tier* the enforcement actually happened.

If a Tier 3 skill keeps catching problems that a Tier 0.5 instruction should have prevented, that's a promotion signal — the instruction needs to move up.

---

*Open Atlas v1.2. See [skill-functions.md](skill-functions.md) for the four function classes, and [hooks.md](hooks.md) for the hook pattern that powers Tier 5.*

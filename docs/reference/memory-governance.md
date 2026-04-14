# Memory Governance

How to manage the memory your AI workspace accumulates over time — what to keep, where to keep it, and when to clean it up.

---

## Two-layer architecture

Workspace memory has two layers with different jobs.

**Working memory** is compact, loaded each session, and guides immediate behavior. It's the set of rules, preferences, and pointers your AI reads at session start. Think of it as the briefing: "here's who you're working with, here's what matters, here's what went wrong last time." Working memory lives in your tool's instruction file (CLAUDE.md, AGENTS.md, .cursorrules, or equivalent) and in any persistent memory mechanism your tool provides.

**Durable memory** is append-only, auditable, and long-term. It's the record of what was learned, what was decided, and what happened. Durable memory lives in your workspace files — learnings logs, decision records, knowledge objects. It doesn't get loaded into every session; it gets queried when relevant.

The key distinction: **working memory is not the durable system of record.** It's a curated projection of durable memory optimized for the AI's context window. And durable memory is not required to be eagerly loaded — it's there when you need it, not competing for attention when you don't.

---

## Memory classes

Not all memory records serve the same purpose. Classifying them helps you decide where things go and when to promote or retire them.

| Class | What it captures | Typical source | Where it lives |
|---|---|---|---|
| **Preference** | Stable user preference that should affect future behavior | Repeated confirmation or explicit instruction | Working memory |
| **Behavior rule** | Rule about how the AI should operate differently | Correction, review finding, or process improvement | Working memory |
| **Routing pointer** | Where to find something or how to navigate the workspace | Discovery during work | Working memory (compact pointer) or durable (full context) |
| **Domain fact** | Specific knowledge about a tool, platform, or constraint | Debugging, experimentation, research | Durable (learnings file) |
| **Decision** | A choice that was made and the reasoning behind it | Decision point during work | Durable (decision record) |
| **Process pattern** | A workflow or approach that worked and should be repeated | Post-session retrospective | Durable (learnings or procedure) |

When something new is discovered, ask: is this a preference (behavioral), a fact (informational), or a decision (intentional)? The answer determines where it belongs.

---

## Promotion rules

Records move from durable to working memory when they earn their way there. Not everything durable deserves a slot in the AI's working context — that space is limited and every entry competes for attention.

**Promote when:**
- A durable record affects behavior in most sessions (not just one domain)
- The same learning keeps being re-discovered because the AI doesn't know about it
- A decision constrains future work and the AI needs to respect it without being reminded
- A routing pointer saves significant time (the AI would otherwise search for the resource every session)

**Don't promote when:**
- The record is domain-specific and only relevant when working in that domain (keep it in a persona or domain-scoped file instead)
- The record is too detailed for the working memory format (keep a pointer, not the full content)
- The record is speculative or low-confidence (validate it first)

Promotion is a deliberate act, not an automatic process. When you run a [retrospective](../reference/learnings.md) or a workspace review, part of the output is identifying which durable records should be promoted.

---

## Feedback lifecycle

Behavioral rules — corrections, confirmations, process improvements — need lifecycle management. Without it, working memory becomes a pile of contradictory instructions that grows until the AI can't follow all of them.

### States

| State | Meaning |
|---|---|
| **Active** | Currently describes desired behavior. Points to a live context. No newer rule has replaced it. |
| **Superseded** | A newer rule has replaced or narrowed this one. Keep the old record (audit trail) but mark it so the AI doesn't follow both. |
| **Retired** | The rule no longer applies — the context changed, the tool changed, the constraint was removed. Archive it. |
| **Orphaned** | The rule points to something that no longer exists (a deleted file, a removed tool, a deprecated feature). Investigate and either update or retire. |

### Supersession

When a new rule replaces an old one, make the replacement legible:
- Link the new record to the one it supersedes
- Mark the old record as superseded
- State what changed and why

The goal is that anyone reading the memory (you or the AI) can follow the chain: "this rule used to say X, now it says Y, because Z changed."

### Periodic review

Every 30 days (or whenever memory feels noisy), scan working memory for:
- Rules that contradict each other (one says "always do X," another says "never do X in this context" — which wins?)
- Orphaned pointers (the file moved, the tool was replaced)
- Low-value entries that clutter without adding signal
- Active rules that haven't been relevant in 30+ days (candidates for demotion back to durable-only)

The review is manual. Read the memory, question each entry, prune what doesn't earn its place. A lean working memory with 15 high-signal entries outperforms a sprawling one with 50 entries the AI skims past.

---

## What NOT to put in memory

Some things look like memory but belong elsewhere:

- **Code patterns and architecture** — these are in the code. Read the code.
- **Git history** — `git log` and `git blame` are authoritative. Don't duplicate them in memory.
- **Debugging solutions** — the fix is in the code; the commit message has the context.
- **Anything already documented** — if it's in a governance file, a template, or a reference doc, don't also put it in memory. One source of truth.
- **Ephemeral task details** — current conversation context, in-progress work state. That's session state, not memory.

Memory captures what's *not obvious from the workspace itself* — the gotchas, the constraints, the preferences, the non-obvious decisions.

---

## Connection to learnings

The [learnings pattern](learnings.md) is the simplest implementation of durable memory. Each learnings entry is a domain fact or process pattern with identity, confidence, and retrieval tags. Memory governance adds the promotion and lifecycle layers on top: which learnings earn a spot in working memory, and how behavioral rules stay current over time.

If you're starting from scratch: begin with a learnings file (durable layer) and your tool's instruction file (working layer). Add lifecycle management when the working layer gets noisy.

---

*Open Atlas v1.2. See [learnings.md](learnings.md) for the durable memory pattern, and [reliability-tiers.md](reliability-tiers.md) for how memory-based rules (Tier 1) compare to other enforcement mechanisms.*

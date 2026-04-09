---
atlas_tier: framework
---

# End-to-End Walkthrough: From Messy Problem to Structured Knowledge

> **Reading this in v1.1?** This walkthrough demonstrates the *prompt-template path* — paste a template, fill in the inputs, get a structured output. The v1.1 production skills in [`workspace/skills/`](../workspace/skills/) automate the same flow with composable strategies and output contracts. For the equivalent skill-driven flow: invoke `think-it-through` (composes `options-and-tradeoffs`) for the analysis, then `capture` for the decision draft, then `extract-knowledge` for the durable insight. Both paths still ship in v1.1 — the prompt templates work for one-off conversations, the skills work for repeatable workflows.

This walkthrough shows how Open Atlas templates work together in practice. Follow along with a real scenario to see the full flow.

---

## The Scenario

You're evaluating whether to use SQLite or PostgreSQL for a new side project. You've read a few articles, had some conversations, and have scattered opinions — but no clear decision.

---

## Step 1: Use the Analysis Template

Open `workspace/templates/prompt/analysis.prompt.md` and give your AI the inputs:

> **Goal:** Decide between SQLite and PostgreSQL for a task management app that starts local but may go multi-user.
>
> **Context:** Solo developer, early-stage project. Currently prototyping. May add a web frontend later.
>
> **Constraints:** No budget for managed database hosting. Must work on macOS and Linux.

The AI follows the analysis procedure — restates the goal, identifies variables, proposes options, compares tradeoffs, and recommends.

**Output:** A structured analysis with options, tradeoffs, and a recommendation. Save it to `drafts/sqlite-vs-postgres-analysis.md`.

---

## Step 2: Record the Decision

The analysis helped you decide: **SQLite for now, migrate to Postgres if you add multi-user.** Use `workspace/templates/doc/decision-record.md` to capture it:

```yaml
---
title: "Database Selection: SQLite for MVP"
doc_type: decision
status: reviewed
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: you
tags: [architecture, database, task-app]
---
```

Fill in the Context, Options, Decision, Rationale, and Risks sections. The template guides you through each one.

**Output:** A decision record saved to `knowledge/decisions/database-selection-sqlite.md`. Now anyone (including future-you and your AI) can understand *why* SQLite, not just *that* SQLite.

---

## Step 3: Extract Durable Knowledge

During the analysis, you learned something worth keeping: **SQLite handles concurrent reads well but struggles with concurrent writes — the threshold is roughly 50 write transactions per second before you need to consider WAL mode or migration.**

That's a reusable insight. Use `workspace/templates/doc/knowledge-object.md`:

```yaml
---
title: "SQLite Concurrency Limits and Migration Triggers"
doc_type: kno
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: you
tags: [database, sqlite, architecture]
source: ai-assisted
confidence: medium
---
```

Write the Thesis ("SQLite is viable for single-user apps up to ~50 write TPS"), the Core Guidance (what, why, how), and the Sources.

**Output:** A knowledge object saved to `knowledge/engineering/sqlite-concurrency-limits.md`. Status: `draft` — you'll promote it to `reviewed` after verifying the numbers.

---

## Step 4: See What Happened

In three steps, a messy "should I use SQLite?" turned into:

| Artifact | Template used | Location | Status |
| --- | --- | --- | --- |
| Structured analysis | `analysis.prompt.md` | `drafts/` | Working draft |
| Decision record | `decision-record.md` | `knowledge/decisions/` | Reviewed |
| Knowledge object | `knowledge-object.md` | `knowledge/engineering/` | Draft (needs review) |

**The key insight:** Each artifact built on the previous one. The analysis informed the decision. The decision surfaced reusable knowledge. The knowledge object will inform future decisions — without re-researching SQLite concurrency from scratch.

This is what "templates enable compounding" means in practice.

---

## What the AI Learns

Next time you face a database decision, your AI can:

1. **Find the decision record** via the blueprint and frontmatter tags
2. **Read your prior reasoning** instead of starting from scratch
3. **Reference the KNO** for SQLite-specific constraints
4. **Use the same templates** to produce consistent, buildable output

Structure compounds. That's the whole idea.

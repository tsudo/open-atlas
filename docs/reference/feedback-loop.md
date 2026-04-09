# Feedback Loop

How Open Atlas separates **system learning** (the workspace getting better at its job) from **training** (pedagogical material for humans). And how POSITIVE_SIGNAL events make the workspace's learning visible.

---

## Vocabulary discipline: training vs learning

Two words that get used interchangeably outside Open Atlas. Inside Open Atlas they mean different things:

- **Training** — pedagogical material for *you*, the human. Lives in `training/`. The kits, prompts, and examples that teach you how to build personas and skills. Static. Authored once, updated when conventions change.
- **Learning** — the workspace's ability to get better at its job over time. Driven by POSITIVE_SIGNAL events, feedback memories, and your periodic audits. Dynamic. Accumulates as you use the system.

The distinction matters because they need different shapes. Training is a curriculum — you read it once and absorb it. Learning is a feedback loop — it runs continuously and produces signal you act on.

When this doc talks about "the feedback loop," it means **learning**, not training.

---

## POSITIVE_SIGNAL events

POSITIVE_SIGNAL is a structured event that gate skills and steward skills emit when something noteworthy happens. The event format is JSONL — one event per line, in a log file the workspace can append to.

**Why "POSITIVE_SIGNAL"?** Because most logging captures *failures*. POSITIVE_SIGNAL is the inverse: a structured record of moments when a skill *worked*, when it caught a real issue, when it prevented something bad. The accumulation of these events is how you can tell whether your skills are earning their place.

### The qualifier vocabulary

Every POSITIVE_SIGNAL event has a qualifier — a controlled vocabulary tag that says *what kind* of positive signal this is. The vocabulary:

| Qualifier | Meaning |
|---|---|
| `worked_as_designed` | Skill ran cleanly on its happy path. Validates the skill is reachable and the procedure works. |
| `caught_real_issue` | Skill found something that mattered — a credential, a drift finding, a real attack. The skill earned its keep. |
| `pass_with_notes_resolved` | Gate skill returned PASS-WITH-NOTES, the user reviewed the notes, and the issue was resolved. |
| `first_use` | First invocation of the skill. Validates end-to-end wiring. |
| `revealed_gap` | Skill found something that pointed at a gap in the skill itself (a missing pattern, a missing check). Drives improvement. |
| `surprising_outcome` | Skill produced output that contradicted user expectation in a useful way ("I thought I had 10 drafts, you found 47"). |

These six are the canonical vocabulary. Don't coin new ones unless none of them fit.

### Example event

```json
{"ts":"2026-04-07T14:32:18Z","skill":"publish-check","qualifier":"caught_real_issue","detail":"BLOCK at config.yml:12 — OpenAI key pattern","severity":"high"}
```

### What the events are good for

- **Trend signal.** Reading the events in sequence tells you whether your skills are accumulating wins or sitting unused.
- **Skill triage.** A skill that hasn't emitted any events in three months is a candidate for retirement.
- **Coverage gaps.** A `revealed_gap` event is a TODO — a missing pattern, a missing check, an edge case the skill didn't handle.
- **Confidence calibration.** If a gate skill keeps emitting `pass_with_notes_resolved`, the threshold for PASS-WITH-NOTES might be too tight (too many notes are resolvable trivially) or too loose (real issues are sliding into PASS-WITH-NOTES instead of BLOCK).

---

## Feedback memories — the human-in-the-loop variant

Skills emit events automatically. **You** also have a feedback channel: feedback memories.

A feedback memory is a structured note about *how* you want the AI to work — corrections, preferences, lessons from incidents. They live wherever your AI tool stores persistent memory (in Claude Code, that's the auto-memory system).

The shape of a good feedback memory:

- **Lead with the rule** — the thing you want the AI to do or not do
- **`Why:`** — the reason. Often a past incident or strong preference. This lets future-you (or future-AI) judge edge cases.
- **`How to apply:`** — when this guidance kicks in.

Feedback memories are the slow-running version of POSITIVE_SIGNAL events. Where events are automatic and per-invocation, feedback memories are deliberate and per-pattern. Together they cover both ends of the loop.

---

## How the loop closes

The feedback loop only matters if it produces action. The cadence:

1. **During work:** skills emit POSITIVE_SIGNAL events automatically; you save feedback memories when you correct the AI on something non-obvious.
2. **Weekly:** run a workspace audit (the `review-context` example skill). Review the events and the maintenance findings. Decide what to act on.
3. **When patterns emerge:** if you keep seeing `revealed_gap` events on the same skill, fix the skill. If you keep saving the same kind of feedback memory, codify it into a hook or a persona rule.
4. **Quarterly (if you ever):** retire skills that have produced no signal. Update the schemas based on what drift you keep finding. Refactor the workspace structure if it's straining against your actual work.

The loop is light by design. Open Atlas does not try to be a learning management system. It tries to make the things worth noticing visible enough that you'll notice them.

---

## What this is not

- **Not telemetry.** POSITIVE_SIGNAL events are local. Nothing leaves your workspace.
- **Not analytics.** There's no dashboard, no aggregation across users, no central server.
- **Not machine learning.** The workspace doesn't fine-tune anything. The "learning" is human-driven adjustments based on patterns the events make visible.
- **Not mandatory.** You can run Open Atlas without ever wiring POSITIVE_SIGNAL emission. The events are an option, not a requirement.

---

## Where to go next

- **The simpler, portable pattern** → [`learnings.md`](learnings.md) — a flat append-only markdown log for cross-session discoveries. This is the lighter-weight cousin of the feedback loop above; use it when you want the pattern without the POSITIVE_SIGNAL infrastructure.
- **The production steward skill** → [`workspace/skills/review-context.md`](../../workspace/skills/review-context.md) — uses event emission as a workspace audit pattern
- **Gate-class discipline (no production gate skill ships in v1.1)** → [`skill-functions.md`](skill-functions.md#gate--forced-verdicts-at-decision-points) — the gate function pattern that would emit events; v1.2 will ship a production gate skill
- **The workspace maintenance cadence** → [../day-2/workspace-maintenance.md](../day-2/workspace-maintenance.md)
- **Why Open Atlas separates training and learning** → [why-this-works.md](why-this-works.md)

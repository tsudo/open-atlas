---
atlas_tier: framework
title: "retro"
doc_type: skill
status: reviewed
created: 2026-04-13
updated: 2026-04-13
owner: open-atlas
tags: [skill, retrospective, learnings, continuous-improvement]
function: workflow
hotwords: [retro, retrospective, session review, what did we learn]
freedom_level: medium
---

# Skill: retro

**Function:** workflow
**Freedom level:** medium
**Composes strategy:** [`structured-extraction`](../templates/strategy/structured-extraction.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `retro`, `retrospective`, `session review`, `what did we learn`

---

## Purpose

Run a structured review at the end of a work session. Surfaces what worked, what failed, and what should be captured — then routes approved items to durable storage. The capture skill gets information into the workspace; this skill closes the loop by reviewing what happened and pulling out the lessons.

---

## What This Skill Does

- Assesses session scope to recommend quick or full mode
- Asks focused questions designed to surface non-obvious learnings
- Classifies findings by type (learning, process improvement, follow-up task)
- Proposes a routing plan for each finding (where it should be stored)
- Writes to durable storage only after explicit user approval

---

## What This Skill Does NOT Do

- Replace the learnings file — this skill feeds it, not manages it
- Run automatically — it's invoked by the user at session end
- Modify workspace governance files
- Make decisions about what's worth capturing — the user decides

---

## Inputs

- **Required:** The current session's context (conversation history, files touched, decisions made)
- **Optional:** Mode argument — `quick` or `full`. If omitted, the skill assesses scope and recommends.

---

## Procedure

### 1. Mode selection

Check if the user specified a mode:
- `quick` → go to step 3
- `full` → go to step 4
- Neither → go to step 2

### 2. Scope assessment (adaptive mode only)

Scan the session for scope signals:

| Signal | Weight |
|--------|--------|
| Number of skills invoked | High |
| Governance or structural files touched | High |
| New artifacts created (knowledge objects, personas, skills) | High |
| Number of files edited | Medium |
| Conversation length | Medium |
| Single-domain vs cross-domain work | Medium |

**Recommendation:** 0-1 high signals → recommend quick. 2+ high signals or 3+ medium → recommend full.

Present the recommendation. Wait for user confirmation before proceeding.

### 3. Quick mode

Ask these four questions, one at a time. Wait for each answer before asking the next.

1. **What worked?** Anything that went smoothly, a tool that performed well, a process that held.
2. **What failed or surprised you?** Unexpected behavior, a workaround you had to find, something that took longer than expected.
3. **What would you do differently?** Process changes, different ordering, tools you'd use next time.
4. **Anything worth writing down?** Specific discoveries, constraints, or decisions that future sessions should know about.

After all four answers, go to step 5.

### 4. Full mode

Walk through these areas systematically:

**4a. Decisions review.** What decisions were made this session? For each: what was decided, what were the alternatives, what was the reasoning? Any decisions that should be recorded in a decision log?

**4b. Process evaluation.** Did the workflow hold? Were there points where the process broke down — steps skipped, gates bypassed, constraints forgotten? What caused the breakdown?

**4c. Root cause analysis.** For any failures or surprises: what was the root cause? Was it a knowledge gap (learning), a process gap (improvement), or a one-off situation (no action needed)?

**4d. Wins and positive signal.** What earned its keep this session? A skill that caught a real issue, a template that saved time, a governance rule that prevented a mistake. These are worth recording so you know what's working, not just what's broken.

**4e. Open items.** Anything deferred, partially completed, or flagged for future work? Each open item needs a destination — task tracker, backlog, or explicit "not worth tracking."

After the walkthrough, go to step 5.

### 5. Synthesize and route

Compile findings into a routing plan. For each finding, propose a destination:

| Finding type | Destination |
|---|---|
| Discovery or pitfall | Learnings file (append new entry with ID, type, confidence, tags) |
| Process improvement | Workspace governance or skill update (describe the change) |
| Follow-up task | Task tracker or backlog (describe the task) |
| Positive signal | Learnings file as a positive entry, or note in relevant skill |
| Decision worth recording | Decision log (if you keep one) |

Present the routing plan as a table. Do not write anything until the user approves.

### 6. Execute routing (after approval)

For each approved item, write to the designated destination:
- Learnings entries use the schema from `docs/reference/learnings.md`
- Follow-up tasks go to whichever task system the user specifies
- Process improvements are described but not auto-applied (the user decides when to implement)

Report what was written and where.

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections:**

- `## Session Summary` — one-paragraph scope description
- `## Findings` — categorized list with proposed routing
- `## Routing Plan` — table of finding → destination mappings
- `## Actions Taken` — what was written and where (after approval)

**Status mapping:**

- `DONE` — all approved items routed to durable storage
- `DONE_WITH_NOTES` — some items routed, others deferred by user choice
- `BLOCKED` — couldn't write to a destination (file missing, permission issue)
- `NEEDS_CONTEXT` — session too short or too unfocused to produce useful findings

---

## Approval Boundaries

- **Before any writes.** The routing plan must be presented and approved before any file is modified or any task is created.
- **Mode confirmation.** In adaptive mode, the recommended mode must be confirmed before the retrospective proceeds.

---

## Authority Boundaries

- Can read any file in the workspace for context
- Can append to the learnings file after approval
- Cannot modify governance files, skill files, or templates
- Cannot create tasks in external systems without explicit instruction
- Routes process improvements as recommendations, not direct edits

---

## Activation

- **Hotwords:** `retro`, `retrospective`, `session review`, `what did we learn`
- **Explicit invocation:** `Run the retro skill`
- **Best timing:** End of a work session, after the main task is complete

---

## BAD vs GOOD

| BAD | GOOD |
|-----|------|
| "That was a productive session!" (no findings) | "Three findings: L-0055 jq truthy gotcha, process gap in cross-ref checking, follow-up task for hook scoping fix." |
| Writes to learnings without asking | "Here's the routing plan. Approve before I write?" |
| "Everything went well, nothing to capture" (after a 2-hour multi-skill session) | "Quick mode might be fine here — short session, single domain. Go with quick?" |

---

## Resources

- `docs/reference/learnings.md` — learnings schema and record hygiene
- `docs/reference/feedback-loop.md` — POSITIVE_SIGNAL events and trend tracking
- `workspace/templates/strategy/structured-extraction.md` — the reasoning strategy this skill composes
- `workspace/templates/output-contract/base.md` — the output envelope
- `workspace/governance/skill-conventions.md` — skill design standards

---

*Created from `workspace/templates/skill/skill-template.md` (Open Atlas v1.2).*

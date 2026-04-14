---
atlas_tier: framework
title: "[Skill Name]"
doc_type: skill
status: draft
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [your-name]
tags: [skill]
function: workflow  # one of: gate | steward | workflow | utility — REQUIRED, no default
hotwords: []        # list of trigger phrases — REQUIRED, no default
freedom_level: medium  # high | medium | low — see skill-conventions.md
---

<!--
SKILL TEMPLATE — Open Atlas v1.1

Fill in the bracketed sections. Delete this comment block when done.

When you (or the skill-starter kit) create a skill FROM this template, the new file
should set `atlas_tier: user` in its frontmatter — not `framework`. This template is
shipped framework content; the skills you create are yours.

If you prefer an AI-guided interview to fill this out, use:
  training/kits/skill-starter/SETUP.md

If you prefer a single paste-into-AI prompt, use:
  training/prompts/create-skill.prompt.md

== Function classification (REQUIRED) ==

Every skill declares one of four function types in its frontmatter. The function determines
which discipline rules apply.

  gate     — blocks downstream work on findings; emits POSITIVE_SIGNAL on clean pass;
             MUST use NO-HEDGING contract; MUST have anti-skip clause
  steward  — owns ongoing care of a surface (review, audit, drift detection);
             emits POSITIVE_SIGNAL on completion; periodic-cadence pattern
  workflow — drives a multi-step process from input to output;
             emits completion status; the most common skill type
  utility  — read-only or single-purpose helper; minimal emissions; no NO-HEDGING required

If you can't decide, default to `workflow` and revise later. `gate` is for skills that
must say PASS / BLOCK; if your skill doesn't produce a forced verdict, it's not a gate.

== Freedom level (RECOMMENDED) ==

Declares how much latitude the AI has during execution. Orthogonal to function
(which measures consequence of failure).

  high    — multiple valid approaches; heuristic guidance; principles over rigid steps
  medium  — preferred pattern exists; some variation acceptable; the default
  low     — fragile operations; specific sequence required; minimal deviation allowed

Default when omitted: gate/steward → low, workflow → medium, utility → high.
Explicit declaration overrides inference.

== Approval boundaries (RECOMMENDED) ==

Declare the points where the skill must pause for explicit user confirmation.
Typical triggers: durable writes, external mutations, routing decisions, mode selection.
If the skill is read-only with no side effects, omit this section.

Forward-testing discipline: after writing the skill, mentally walk through "what happens
if the AI interprets this instruction in the most permissive way possible?" If that
produces bad outcomes, tighten the freedom level or add approval boundaries.
-->

# Skill: [Name]

**Function:** [gate | steward | workflow | utility]
**Freedom level:** [high | medium | low — see `workspace/governance/skill-conventions.md`]
**Composes strategy:** [pick one from `workspace/templates/strategy/` if your skill does reasoning work, or omit for pure utilities]
**Composes contract:** [base output contract](../output-contract/base.md)
**Hotwords:** `[trigger phrase 1]`, `[trigger phrase 2]`

---

## Purpose

[2-3 sentences. What this skill is for. Be concrete — "helps with X" is too vague; "takes raw input, classifies it against the workspace template catalog, and saves a draft with frontmatter to drafts/" is right.]

---

## What This Skill Does

[Bulleted list of 3-7 specific things this skill reliably produces or enforces. Each item should be actionable enough that a colleague reading it knows exactly what to expect.]

- [Action 1]
- [Action 2]
- [Action 3]

---

## What This Skill Does NOT Do

[Bulleted list of 3-5 explicit non-responsibilities. REQUIRED. A skill without clear non-responsibilities will scope-creep into work it isn't designed for.]

- [Non-responsibility 1]
- [Non-responsibility 2]
- [Non-responsibility 3]

---

## Inputs

[What the skill needs to do its work. Be specific about format and source.]

- **Required:** [input 1 — what it is, where it comes from]
- **Optional:** [input 2, with defaults]

---

## Procedure

[Numbered steps. Each step is one concrete action the skill takes. The procedure is the skill's runbook — anyone (or any AI) executing the skill should be able to follow these steps mechanically.]

1. [Step 1]
2. [Step 2]
3. [Step 3]
4. ...

---

## Output Contract

**Composes:** [base output contract](../output-contract/base.md)

**Required body sections** (in this order — these are the skill-specific sections that go inside the base envelope):

- `## [Section name 1]` — [one-line description of what goes here]
- `## [Section name 2]` — [one-line description]
- `## [Section name 3]` — [one-line description]

**Status mapping:**

- `DONE` — [the condition under which this skill returns DONE]
- `DONE_WITH_NOTES` — [the condition for DONE_WITH_NOTES — soft warnings, partial successes the user should know about]
- `BLOCKED` — [the condition for BLOCKED — external blocker, missing input, tool unavailable]
- `NEEDS_CONTEXT` — [the condition for NEEDS_CONTEXT — clarification needed before proceeding]

The base contract handles the universal envelope (frontmatter, summary, notes, status). This section defines only what's *unique* to this skill. The skill MUST return one of the four statuses. "Sort of done" is not a valid output.

<!--
== NO-HEDGING contract (REQUIRED for `gate` skills, optional for others) ==

Delete this section if function != gate. Otherwise, fill it in.

Gate skills produce forced verdicts. The valid verdict vocabulary is:

  PASS              — no findings, ship it
  PASS-WITH-NOTES   — findings exist but don't block; user should know
  BLOCK             — at least one finding requires a fix before proceeding
  SKIPPED           — gate could not run; surfaces the reason; never silently passes

The skill MUST pick one of these four. "Looks fine," "probably good," "seems okay" are
not valid verdicts. Hedged language defeats the purpose of having a gate.
-->

## NO-HEDGING Contract

[Delete this section if function != gate.]

This skill produces a forced verdict from the set: `PASS | PASS-WITH-NOTES | BLOCK | SKIPPED`.

- **PASS** — no findings
- **PASS-WITH-NOTES** — findings exist but don't block; user should review
- **BLOCK** — at least one finding requires a fix before proceeding
- **SKIPPED** — gate could not run; reason cited; never silently passes

Every finding MUST cite specific evidence: file path, line number, exact phrase, or precise location. Findings without evidence are noise.

The skill cannot silently bypass its own checks. If a check cannot run, the skill emits SKIPPED with the reason — not PASS.

---

## Event Emission

[Delete this section if function is `workflow` or `utility`.]

This skill emits POSITIVE_SIGNAL events to the workspace's learnings log on the following triggers:

- **`worked_as_designed`** — skill completed cleanly with no findings or surprises
- **`caught_real_issue`** — skill found a confirmed problem the user would have missed
- **`pass_with_notes_resolved`** — skill flagged a soft warning that turned out to matter
- **`first_use`** — first invocation of this skill (validates the skill works end-to-end)
- **`revealed_gap`** — skill execution surfaced a gap in another skill, template, or convention
- **`surprising_outcome`** — skill produced an output the user didn't expect (worth investigating)

Events are appended to your workspace's learnings log. See `docs/reference/feedback-loop.md` for the broader pattern.

---

## Activation

[How is this skill invoked? Be specific.]

- **Hotword(s):** `[trigger phrase]`, `[other trigger]`
- **Explicit invocation:** `Run the [Name] skill`
- **Tool wiring:** `[Claude Code slash command, Cursor shortcut, etc. — depends on your tool]`

---

## BAD vs GOOD

[Required for `gate` skills. Optional for others. Show 1-2 BAD/GOOD pairs that demonstrate the skill's discipline.]

| BAD | GOOD |
|-----|------|
| [bad output example] | [good output example] |

---

## Authority Boundaries

- [What this skill can and cannot do without explicit user approval]
- [What it never modifies]
- [What it routes to humans]

---

## Approval Boundaries

[Points where the skill must stop and wait for explicit user confirmation before continuing. Delete this section if the skill runs fully autonomously with no mutation side effects.]

- [Boundary 1 — e.g., "Before writing any files to workspace/knowledge/"]
- [Boundary 2 — e.g., "Before creating tasks in external systems"]
- [Boundary 3 — e.g., "After presenting the routing plan but before executing it"]

---

## Resources

- `workspace/templates/strategy/` — five reasoning strategy templates skills can compose (`adversarial-critique`, `chain-of-thought`, `options-and-tradeoffs`, `self-refine`, `structured-extraction`)
- `workspace/templates/output-contract/base.md` — the base output envelope this skill composes
- `workspace/governance/skill-conventions.md` — how skills are organized in this workspace
- `workspace/templates/persona/voice-quality.md` — voice rules every skill output respects
- `docs/reference/skill-functions.md` — full reference on the four function types (gate / steward / workflow / utility)
- `docs/reference/feedback-loop.md` — POSITIVE_SIGNAL events, completion status protocol, learnings aggregation
- `docs/reference/output-contracts.md` — the discipline behind output contracts

---

*Created from `workspace/templates/skill/skill-template.md` (Open Atlas v1.2).*

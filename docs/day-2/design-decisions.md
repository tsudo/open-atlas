# Design Decisions

A structured approach for workspace architecture decisions — when a change needs more than a quick edit.

---

## When to use this

Most workspace changes are small: add a learning, edit a skill, update a template. Those don't need a formal process. Just do them.

This process is for structural changes:
- Adding a new governance layer or convention
- Changing how skills, personas, or templates work
- Restructuring directories or file ownership
- Introducing a new pattern that affects multiple files
- Anything where getting it wrong means a painful rollback

The test: if the change touches one file, do it directly. If it touches five files and changes how things connect, run it through stages.

---

## The stages

### 1. Frame the problem

State what's wrong or missing. Use evidence, not intuition.

- What specifically isn't working? (Name the failure, not the feeling.)
- How often does it happen? (One-off vs recurring determines urgency.)
- What's the cost of doing nothing? (If the answer is "nothing," reconsider whether this needs a decision at all.)

Write this down. Two to four sentences. If you can't state the problem concisely, you don't understand it well enough to solve it.

### 2. Generate options

List at least two viable approaches. For each option:

| Field | What to write |
|---|---|
| **Summary** | One sentence: what this option does |
| **Pros** | What it makes better |
| **Cons** | What it makes worse or harder |
| **Effort** | S, M, or L — how much work to implement |
| **Reversibility** | Easy, moderate, or hard to undo |

Force yourself to include a "do nothing" option. Sometimes the current state is acceptable and the effort of changing it isn't justified.

### 3. Stress-test

For each option, ask adversarial questions:

- What breaks if we pick this? What files, skills, or conventions would need to change?
- What happens in six months? Does this option create maintenance debt or lock us into something?
- What's the failure mode? If this option fails, how do we find out, and how bad is it?
- Who else is affected? If other people use this workspace, does this change their workflow?

If you have access to a second AI model or a fresh session, ask it to review your options independently. Same-model blind spots are real — an independent review catches assumptions the author can't see.

### 4. Draft the decision record

Use the [decision record template](../../workspace/templates/doc/decision-record.md) to capture:

- The problem statement (from stage 1)
- Options considered (from stage 2)
- The decision and reasoning
- Consequences — what changes, what doesn't, what to watch for

The decision record is not a formality. It's the artifact that future-you reads when asking "why did we do it this way?" Write it for that audience.

### 5. Get approval

If you're the sole user: read the decision record cold (pretend you're seeing it for the first time) and check whether the reasoning holds. If it does, approve it yourself. If it doesn't, you found a gap — go back to stage 2 or 3.

If others are affected: share the decision record and get explicit agreement before implementing. Decisions that affect shared workspace conventions need shared buy-in.

### 6. Implement

Make the change. Follow the [pipeline](project-pipeline.md) if the implementation is M or L sized. Update the workspace BLUEPRINT if the change adds or moves directories.

---

## Lightweight vs full process

Not every decision needs all six stages. Scale the ceremony to the consequence.

| Consequence | Process |
|---|---|
| Affects one file, easily reversed | Just do it. No decision record needed. |
| Affects several files, moderate effort | Stages 1, 2, 4 (frame, options, record). Skip adversarial if the choice is obvious. |
| Affects workspace structure, hard to reverse | All six stages. Independent stress-test. Full decision record. |

The goal is to match decision rigor to decision weight. A naming convention change that touches 30 files deserves more thought than a one-line config edit, even though neither involves "code."

---

## Common patterns

**"I keep making the same mistake."** That's a signal to promote a rule, not to restructure the workspace. Check [reliability tiers](../reference/reliability-tiers.md) — the rule might just need to move from Tier 0.5 to Tier 3 or 5.

**"This convention doesn't work anymore."** Good candidate for this process. Frame what changed (stage 1), propose alternatives (stage 2), and record the switch (stage 4) so future-you knows why the old convention was retired.

**"I want to add a feature the framework doesn't have."** Start by checking if a lighter version already exists (a skill, a template section, a governance note). If not, design the addition through stages 1-5, build it through the pipeline, and register it in the BLUEPRINT.

---

*Open Atlas v1.2. See [project-pipeline.md](project-pipeline.md) for the build process, and the [decision record template](../../workspace/templates/doc/decision-record.md) for the artifact format.*

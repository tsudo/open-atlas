# Creating Skills

A skill is a defined workflow the AI can execute. This doc covers when to add one, how to pick the right function class, and how to wire it.

---

## When to add a skill

Add a skill when you've done the same multi-step thing more than twice and want it to be reliable. Concretely:

1. **You have a workflow** — multiple steps, in a defined order, with a defined output.
2. **You want the AI to do it the same way every time** — not "approximately" the same, but the *same* way.
3. **You're willing to write the procedure down** — because that's what the skill is.

Don't make a skill for one-time work. Don't make a skill for "be smart about X." Skills are for repeated, defined workflows.

---

## The four function classes

Every skill belongs to exactly one function class. Pick the right one and the rest of the skill writes itself.

| Class | When to use | Discipline |
|---|---|---|
| **`gate`** | Forced verdicts at decision points | NO-HEDGING contract, PROOF gate, anti-skip clause, forced verdicts (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED) |
| **`steward`** | Periodic ongoing care of a surface | POSITIVE_SIGNAL emission, cadence (not on-demand) |
| **`workflow`** | Multi-step process from input to output | The most common class. Procedure + output contract. |
| **`utility`** | Read-only helper | Lightweight. Just inputs → output. |

If you're not sure, default to `workflow`. The other three are specializations of the same base shape.

See [../reference/skill-functions.md](../reference/skill-functions.md) for each class in depth.

---

## Two ways to make one

### Option A: Copy the starter scaffold

The fastest path. Copy `workspace/skills/my-next-skill.md`, rename it, fill in the sections. The scaffold tells you which fields to fill in and points at production skills as references for each section type.

### Option B: The kit interview

Use `training/kits/skill-starter/SETUP.md`. Structured AI-guided interview through:

- Function classification (with hotword conflict checking)
- Procedure design
- Output contract
- Function-specific requirements (NO-HEDGING for gates, event emission for stewards/gates)
- Verification against `VERIFY.md`

### Option C: The single-pass prompt

Use `training/prompts/create-skill.prompt.md`. Faster than the kit, requires you to know which function class fits.

### Read a production skill that's close to what you want to build

Open Atlas v1.1 ships four production skills in `workspace/skills/` — they are not example files, they are working skills, but they are also written to be readable. Pick the one closest to what you want to build:

- **Workflow skill?** Read `workspace/skills/capture.md` (simplest) or `workspace/skills/extract-knowledge.md` (durability discipline)
- **Workflow skill that handles ambiguity?** Read `workspace/skills/think-it-through.md` (anti-sycophancy gates)
- **Steward skill?** Read `workspace/skills/review-context.md` (cadence framing, drift checks)
- **Gate skill?** Read `docs/reference/skill-functions.md` for the gate-class discipline. v1.1 does not ship a production gate skill; the pattern is documented in the reference.

Studying the production skills is the highest-leverage learning path for building your own.

---

## Skills compose reasoning strategies and output contracts

Open Atlas ships five reasoning strategy templates in `workspace/templates/strategy/` (`adversarial-critique`, `chain-of-thought`, `options-and-tradeoffs`, `self-refine`, `structured-extraction`) and a base output contract template in `workspace/templates/output-contract/base.md`. **Skills compose these instead of reinventing how to reason or how to format their output.**

When you write your own skill, ask:

- *Which strategy fits the reasoning shape this skill needs?* Compose it. Don't reinvent it.
- *Does the base output contract envelope work for my output?* Default to yes — compose it. Define the body sections that are unique to your skill; inherit the envelope.

Each production skill in `workspace/skills/` demonstrates this pattern. Reading them shows what compose-the-strategy and compose-the-contract look like in practice.

---

## The shape of a good skill

Every skill file has these sections (enforced by `workspace/templates/skill/skill-template.md`):

| Section | Required? | What goes here |
|---|---|---|
| **Frontmatter** | Always | `atlas_tier: user`, `function:`, `hotwords: [...]` |
| **Purpose** | Always | 2–3 sentences. What the skill is for. |
| **What This Skill Does** | Always | 3–7 specific actions. |
| **What This Skill Does NOT Do** | Always | At least 3 specific non-responsibilities. |
| **Inputs** | Always | Required vs optional, with formats. |
| **Procedure** | Always | Numbered steps, mechanical. |
| **Output Contract** | Always | What the skill produces. Be specific. |
| **Completion Status** | Always | One of `DONE` / `DONE_WITH_NOTES` / `BLOCKED` / `NEEDS_CONTEXT` |
| **NO-HEDGING Contract** | Gate only | Forced verdicts, PROOF gate, anti-skip clause |
| **Event Emission** | Gate + steward | POSITIVE_SIGNAL qualifiers and triggers |
| **Activation** | Always | Hotwords + invocation pattern. |
| **Authority Boundaries** | Always | What it can/cannot decide. |

The "does NOT do" section, the procedure section, and the output contract are the load-bearing pieces. If you skimp on those, the skill won't work reliably.

---

## Wiring the skill into your AI tool

Writing the file is half the work. The other half is making the AI actually invoke it.

| Tool | How |
|---|---|
| **Claude Code** | Add a slash command in `.claude/settings.local.json`, OR reference the skill file in `.claude/CLAUDE.md` so it loads at session start |
| **Cursor** | Reference the file in `.cursorrules` |
| **Codex** | Reference the file in `AGENTS.md` |
| **Generic** | Tell the AI to read the skill file at the start of any session that might need it |

Open Atlas does not depend on any of these. The skill files work fine as "read this file" instructions you give the AI manually. Wiring is polish that turns "read this file" into a hotword or a slash command.

---

## How to test a new skill

1. **Invoke it via the hotword.** Does the AI find the skill and start the procedure?
2. **Run it on its happy-path input.** Does it produce the output contract exactly?
3. **Run it on a degraded input** (missing field, ambiguous content). Does it return `NEEDS_CONTEXT` or `BLOCKED` instead of guessing?
4. **For gates: try to make it hedge.** Does it hold the line on the forced verdict, or does it slip into "looks mostly fine"?
5. **For stewards: run it on a clean state.** Does it still produce a report, or does it silently say "all good"? (Silence is not an output.)

If any of these fail, the skill file needs more constraint. The same diagnostic applies as for personas: don't blame the AI — fix the file.

---

## Common failure modes

- **The procedure is too vague.** "Review the file and make it better" is not a procedure. "Read the file, check each section against the rubric in `X`, return findings as a list" is.
- **The output contract is missing.** Without a defined output shape, the skill produces inconsistent results.
- **No completion status.** The skill ends ambiguously and the AI moves on to the next thing without you knowing what state the work is in.
- **A workflow skill that should be a gate skill.** If your workflow ends in a yes/no decision, it's a gate. Use the gate discipline.
- **A "kitchen sink" skill.** A single skill that does five different things is five skills. Split it.
- **No hotwords, or hotwords that collide with existing skills.** Run the conflict check in the kit's SETUP.md.

---

## Where to go next

- **Build your first skill** → `training/kits/skill-starter/SETUP.md`
- **Build subsequent skills faster** → `training/prompts/create-skill.prompt.md`
- **Understand the function classes in depth** → [../reference/skill-functions.md](../reference/skill-functions.md)
- **Understand the output contract discipline** → [../reference/output-contracts.md](../reference/output-contracts.md)
- **Compare to personas** → [creating-personas.md](creating-personas.md). Skills are *what* the AI is doing. Personas are *who* the AI is being.

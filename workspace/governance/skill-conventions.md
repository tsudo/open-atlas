# Skill Conventions

How skills are organized, named, registered, and invoked in an Open Atlas workspace.

This is a one-page reference. The longer treatment of each function type lives in `docs/reference/skill-functions.md`. The output contract concepts live in `docs/reference/output-contracts.md`. The feedback loop pattern lives in `docs/reference/feedback-loop.md`.

---

## Where Skills Live

| Location | What goes there |
|---|---|
| `workspace/skills/` | Skills you create (default `atlas_tier: user`) |
| `workspace/templates/skill/skill-template.md` | The hardened template you create skills from |
| `training/kits/skill-starter/` | The AI-guided creation kit + worked examples |
| `training/kits/skill-starter/examples/` | Three reference skills demonstrating the function types |

User-created skills are `atlas_tier: user` — Atlas never touches them on upgrade. Shipped framework skills (templates, kit examples) are `atlas_tier: framework`.

## File Naming

- One skill per file: `{hotword-or-name}.md`
- Lowercase, hyphen-separated for multi-word names: `publish-check.md`, `review-context.md`
- Avoid generic names (`helper.md`, `util.md`) — they conflict with future skills

For skills with multiple files (rare), use a directory: `workspace/skills/{name}/SKILL.md` plus supporting files.

## Required Frontmatter

Every skill MUST have:

```yaml
---
atlas_tier: user        # or framework if shipped
title: [Name]
doc_type: skill
status: draft           # promoted to reviewed/canonical by humans only
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [your-name]
tags: [skill]
function: workflow      # gate | steward | workflow | utility — REQUIRED
hotwords: [trigger]     # at least one trigger phrase — REQUIRED
freedom_level: medium   # high | medium | low — RECOMMENDED (defaults inferred from function if omitted)
---
```

Skills missing `function:` or `hotwords:` are incomplete and should not be invoked.
### Freedom Level

Declares how much latitude the AI has during execution. See `docs/reference/skill-functions.md` for the full reference.

| Level | Meaning | Default for |
|---|---|---|
| `high` | Multiple valid approaches | `utility` |
| `medium` | Preferred pattern, some variation | `workflow` |
| `low` | Specific sequence required | `gate`, `steward` |

**Size ceiling:** Keep skill files under 500 lines. Extract stable reference material into subfiles.

**Forward-testing:** After writing a skill, ask "what happens if the AI applies the most permissive interpretation?" Tighten freedom level or add approval boundaries if the answer is bad outcomes.

## The Four Function Types

| Function | What it does | When to use | Discipline |
|---|---|---|---|
| **gate** | Blocks downstream work on findings | Pre-publish checks, security reviews, code reviews | NO-HEDGING contract + anti-skip + forced verdicts (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED) |
| **steward** | Ongoing care of a surface | Workspace audits, drift detection, weekly reviews | Periodic cadence, POSITIVE_SIGNAL emission on completion |
| **workflow** | Multi-step process from input to output | Capture, transform, route, generate | Completion status (DONE / DONE_WITH_NOTES / BLOCKED / NEEDS_CONTEXT) |
| **utility** | Read-only helper | Lookups, listings, formatting | Minimal — no NO-HEDGING required, no POSITIVE_SIGNAL emission required |

**Default when uncertain:** `workflow`. Most skills are workflows. `gate` is for skills that produce forced verdicts; if your skill doesn't, it's not a gate.

## Hotword Conflict Rules

Two skills cannot share a hotword in the same workspace. Before adding a new hotword, check:

```bash
grep -r "hotwords:" workspace/skills/
```

If a hotword collides, pick a different one or scope the new skill more narrowly.

## Registration

After creating a new skill, update `workspace/BLUEPRINT.md` to register it. The blueprint is your AI's map; if a skill isn't in the blueprint, your AI won't know to look for it.

The kit's VERIFY.md includes this check.

## Invocation

How you invoke a skill depends on your AI tool:

| Tool | How |
|---|---|
| **Claude Code** | Configure as a slash command in `.claude/settings.local.json` (or just paste the skill file content into your prompt) |
| **Cursor** | Reference the skill file in `.cursorrules` |
| **Codex** | Reference in `AGENTS.md` |
| **Generic AI tool** | Paste the skill file into your prompt; the procedure section is the runbook the AI follows |

The skill file is the contract. Your AI tool is the runtime.

## Lifecycle

Skills follow the standard lifecycle:

```
draft → reviewed → canonical → archived
```

- **draft** — created, not yet validated
- **reviewed** — used a few times, works as expected
- **canonical** — battle-tested, you trust it without re-reading
- **archived** — superseded or no longer needed; retained for history

Promote based on actual use, not optimism. A skill you've never invoked is a draft, no matter how nicely it's written.

## When NOT to Create a Skill

- If the work is one-off, do it directly. Skills are for repeating patterns
- If a hotword would conflict with an existing skill and the new one isn't a clear improvement, extend the existing skill instead
- If the procedure is "ask the AI nicely," you don't need a skill — that's just a prompt
- If the skill would only be useful to one person on one project, keep it as a personal note instead

The maintenance cost of a skill is real. Every skill in `workspace/skills/` is one more thing your AI loads and one more thing that can drift out of date.

---

*Open Atlas v1.2. See `docs/reference/skill-functions.md` for the longer treatment of each function type, and `docs/reference/output-contracts.md` for the output contract concept that grounds the gate/steward discipline.*

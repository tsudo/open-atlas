---
atlas_tier: framework
title: Persona Starter Kit — VERIFY
doc_type: kit-verify
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [kit, persona, verify, post-creation]
---

# Persona Starter Kit — VERIFY

Post-creation checks for personas produced by `SETUP.md`. Run this against any new persona before declaring the kit complete. Each check is yes/no — if any check fails, fix it before shipping the persona.

This file is also useful as a standalone audit when you suspect an existing persona is incomplete.

---

## How to Run

Point your AI at the new persona file and ask it to walk through this checklist. Each item should resolve to PASS or FAIL. Any FAIL is a blocker — fix it.

Or run it manually: open the persona, scroll through, check each box.

---

## Checks

### Required structure

- [ ] **All required sections present.** The persona has: Purpose, What This Persona Does, What This Persona Does NOT Do, Core Perspective, Mode(s), Default Strategy, Activation Routing, Decision Style, Tone, Authority Boundaries, How to Use This Persona.
- [ ] **No TODO or placeholder text.** Every bracketed section from the template has been filled in. No `[fill this in]`, no `[TBD]`, no leftover comment blocks.

### Frontmatter

- [ ] **Frontmatter is valid YAML.** Opens with `---`, closes with `---`, parses cleanly.
- [ ] **`atlas_tier: user`** is set in the frontmatter. NOT `framework`. This is critical — framework tier means future Atlas upgrades will overwrite the persona; user tier means Atlas leaves it alone.
- [ ] **`title:`** field is present and matches the persona name.
- [ ] **`doc_type: persona`** is set.
- [ ] **`status: draft`** initially (the human can promote later).
- [ ] **`created:`** and **`updated:`** dates are present and valid (YYYY-MM-DD format).
- [ ] **`tags:`** field includes at least `[persona]`.

### Activation routing (the critical section)

- [ ] **Activation routing is concrete.** It specifies at least one of: hotword phrase(s), zone path glob(s), or explicit invocation phrase. NOT "the AI will know when to use it."
- [ ] **Hotword phrases (if used) are unique.** They don't conflict with existing personas in the workspace. Quick check: `grep -l "Activation:" workspace/personas/per_*.md` and skim for collisions.
- [ ] **Zone matches (if used) are valid paths.** The paths exist or are intentional future paths.

### Non-responsibilities (the most-skipped section)

- [ ] **`What This Persona Does NOT Do` section exists and is filled in.** Not "TBD," not "I'll figure this out later."
- [ ] **At least 3 specific non-responsibilities** are listed. Each one is concrete enough to be actionable — "doesn't make architecture decisions, those are the human's domain" is good; "isn't perfect" is filler.
- [ ] **At least one explicit "doesn't modify X" rule** is present, where X is a specific file, directory, or category.

### Voice quality

- [ ] **Tone section references** `workspace/templates/persona/voice-quality.md` (or explicitly notes a deviation with rationale).
- [ ] **No banned vocabulary** appears in the persona's own text. Quick scan for: "delve," "dive into," "leverage," "robust," "seamless," "synergy," "holistic," "paradigm," "empower," "revolutionize," "transform." Personas that *use* banned words in their own description tend to *produce* banned words in their output.
- [ ] **No sycophantic language** in the persona's tone description (e.g., "always validates the user's input first" — this is a sycophancy risk and should be rewritten).

### Workspace integration

- [ ] **Persona file is at the correct path:** `workspace/personas/per_{name}.md` where `{name}` is a lowercase slug.
- [ ] **BLUEPRINT.md is updated** to register the new persona (or a note is left for the human to do this manually).
- [ ] **Wiring instructions** for the user's AI tool are surfaced. The user knows how to activate the persona in their specific tool.

### Sanity check

- [ ] **The persona is genuinely needed.** This is a soft check, but worth doing: is there an existing persona that already covers this scope? If yes, the new persona should either extend the existing one (different work) or replace it (better version), not duplicate it.
- [ ] **The persona has a clear "first use case."** The user can articulate one specific situation where they will invoke this persona within the next week. Personas without a first use case tend to become orphans.

---

## What to Do If Checks Fail

| Failure | Fix |
|---|---|
| Missing section | Re-run the relevant SETUP.md interview step and capture the answer |
| `atlas_tier: framework` instead of `user` | Edit the frontmatter to `user`. Critical — don't ship with this wrong |
| Vague activation routing | Push the user for specifics. If they can't specify a trigger, the persona isn't ready to ship |
| Non-responsibilities empty or vague | Push for at least 3 specific items with at least one "doesn't modify X" rule |
| Banned vocabulary in tone section | Rewrite using the BAD/GOOD examples in `voice-quality.md` |
| Hotword conflict with existing persona | Pick a different hotword or scope the new one to a zone |
| BLUEPRINT not updated | Update it (or surface the gap to the human) |

---

## Output Format

When VERIFY runs, it should produce one of three results:

- **PASS** — all checks pass, persona is ready to ship
- **PASS-WITH-NOTES** — all required checks pass, but at least one soft check (sanity, integration) failed and the user should know
- **BLOCK** — at least one required check failed, persona cannot ship until fixed

Per the NO-HEDGING discipline in `docs/reference/anti-patterns.md`: do not say "looks mostly fine" or "probably good." Pick one of the three verdicts, cite the specific check(s) that drove it.

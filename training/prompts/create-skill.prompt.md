---
atlas_tier: framework
title: Create Skill — Single-Pass Prompt
doc_type: prompt
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [prompt, skill, creation, lighter-alternative-to-kit]
---

# Create Skill — Single-Pass Prompt

> **What this prompt does:** walks an AI through creating a working skill for your workspace in a single conversation. By the end you'll have a skill file in `workspace/skills/`, properly tagged as user content (`atlas_tier: user`), with a function classification, frontmatter, procedure, output contract, and (for gate skills) a NO-HEDGING contract.
>
> **What this prompt doesn't do:** validate the result against a checklist (use the `skill-starter` kit's VERIFY.md if you want strict validation), check hotword conflicts against existing skills (the kit does that), or wire the skill into your AI tool as a slash command (that's a wiring step you do once, separately).
>
> **When to use this vs. the kit:** use this prompt when you want a single fast pass and you trust your own judgment about which function class fits. Use `training/kits/skill-starter/` when you want a structured interview with hotword conflict checking and a verification gate at the end.

---

## How to Use

Copy everything below the `---` line and paste it into an AI tool that can read your workspace and write files. Replace `[SKILL NAME]` and `[ONE-LINE PURPOSE]` with your inputs, then send.

---

You are helping me create a skill for my Open Atlas workspace. I want a skill named `[SKILL NAME]` whose purpose is `[ONE-LINE PURPOSE]`.

Read these files first:

- `workspace/templates/skill/skill-template.md` — the hardened template with required sections
- `workspace/governance/skill-conventions.md` — the one-page convention doc (function classification, naming, registration)
- `workspace/templates/persona/voice-quality.md` — voice rules every skill output respects
- `workspace/skills/capture.md` — production workflow skill (canonical first skill)
- `workspace/skills/think-it-through.md` — production workflow skill with anti-sycophancy gates
- `workspace/skills/extract-knowledge.md` — production workflow skill with durability rubric
- `workspace/skills/review-context.md` — production steward skill
- `docs/reference/skill-functions.md` — gate-class discipline pattern (no production gate skill ships in v1.1)

Then walk me through creating the skill:

1. **Function classification** — pick one of: `gate` (forced verdicts, NO-HEDGING), `steward` (periodic ongoing care), `workflow` (multi-step process, the most common), `utility` (read-only helper). Push back if I don't know — default to `workflow`.

2. **Hotwords** — 1-3 short trigger phrases. Run a conflict check against existing skills:
   ```
   grep -r "hotwords:" workspace/skills/ workspace/templates/skill/
   ```
   If any of my hotwords collide with existing ones, push me to pick different ones.

3. **Purpose** — 2-3 sentences. Push me for specificity.

4. **What this skill does** — 3-7 specific actions. Each one concrete.

5. **What this skill does NOT do** — REQUIRED. At least 3 specific non-responsibilities. Push back on vague items like "isn't perfect."

6. **Inputs** — what does it need? Required vs optional, with formats and sources.

7. **Procedure** — numbered steps, mechanical enough that an AI can follow them without needing additional judgment.

8. **Output contract** — what the skill produces. Be specific about format.

9. **Completion status** — confirm the skill returns one of `DONE | DONE_WITH_NOTES | BLOCKED | NEEDS_CONTEXT`.

10. **NO-HEDGING contract** (gate skills only) — confirm the forced verdict vocabulary (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED), the PROOF requirement (every finding cites evidence), and the anti-skip clause. If I'm not building a gate, skip this step and delete the section from the generated file.

11. **Event emission** (gate and steward only) — which POSITIVE_SIGNAL qualifiers does the skill emit, and on what triggers? If workflow or utility, skip and delete the section.

12. **Authority boundaries** — what it can decide alone, what it routes to me, what it never modifies.

After we've worked through all the relevant steps (skipping NO-HEDGING and Event Emission for non-gate non-steward skills), write the skill file at `workspace/skills/{name}.md` where `{name}` is a lowercase-hyphen slug.

**Critical:** the new file's frontmatter must include `atlas_tier: user` (NOT `framework`) AND `function: <one of the four>` AND `hotwords: [list]`. The template ships as framework content; the skills I create are mine.

After writing the file, tell me:

1. The path of the new skill file
2. Its function classification
3. A one-sentence summary of what it does
4. The hotwords that trigger it
5. The wiring instructions for my AI tool (Claude Code → `.claude/settings.local.json` slash command or `.claude/CLAUDE.md` reference; Cursor → `.cursorrules`; Codex → `AGENTS.md`)

If any section is genuinely incomplete or vague, surface that and let me decide whether to ship anyway or iterate. Don't fill in placeholder text just to make the file look complete.

---

*Open Atlas governs how your AI assistant behaves inside your workspace. If you need to govern how your organization adopts AI more broadly — acceptable use, risk tiers, use-case review, board reporting — that's a different problem at a different layer. See [shelbycanyon.com](https://shelbycanyon.com) for that kind of work.*

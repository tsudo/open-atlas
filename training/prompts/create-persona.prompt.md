---
atlas_tier: framework
title: Create Persona — Single-Pass Prompt
doc_type: prompt
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [prompt, persona, creation, lighter-alternative-to-kit]
---

# Create Persona — Single-Pass Prompt

> **What this prompt does:** walks an AI through creating a working persona for your workspace in a single conversation. By the end you'll have a `per_{name}.md` file in `workspace/personas/`, properly tagged as user content (`atlas_tier: user`), with all required sections filled in.
>
> **What this prompt doesn't do:** validate the result against a checklist (use the `persona-starter` kit's VERIFY.md if you want strict validation), manage multi-persona workspace integration (it just creates one file), or activate the persona in your AI tool (that's a wiring step you do once, separately).
>
> **When to use this vs. the kit:** use this prompt when you want a single fast pass and you trust your own judgment about what makes a good persona. Use `training/kits/persona-starter/` when you want a structured interview with a verification gate at the end. Same destination; different paths.

---

## How to Use

Copy everything below the `---` line and paste it into an AI tool that can read your workspace and write files (Claude Code, Cursor, Codex, or similar). Replace `[PERSONA NAME]` and `[ONE-LINE PURPOSE]` with your inputs, then send.

---

You are helping me create a persona for my Open Atlas workspace. I want a persona named `[PERSONA NAME]` whose purpose is `[ONE-LINE PURPOSE]`.

Read these files first to understand the structure:

- `workspace/templates/persona/persona-template.md` — the template with required sections
- `workspace/templates/persona/voice-quality.md` — the voice rules every persona respects
- `workspace/personas/per_open-atlas.md` — the existing starter persona, as a structural reference

Then walk me through filling in each section of the template:

1. **Purpose** — 2-3 sentences. Push me for specificity.
2. **What this persona does** — 3-7 specific responsibilities. Each one concrete enough to be actionable.
3. **What this persona does NOT do** — REQUIRED. At least 3 specific non-responsibilities. At least one explicit "doesn't modify X" rule. Push back if I give you vague items like "isn't perfect."
4. **Core perspective** — 3-5 mental models or biases. Help me find them if I'm stuck.
5. **Mode(s)** — single or multi-mode. If multi, each mode needs a clear trigger.
6. **Default strategy** — one of: chain-of-thought, options-and-tradeoffs, structured-extraction, adversarial-critique, self-refine.
7. **Activation routing** — how does this persona fire? Push me to specify hotword phrase(s), zone match(es), or explicit invocation. Don't accept "the AI will know."
8. **Decision style** — how willing to push back, how much hedging is okay, recommendations vs options.
9. **Tone** — formality, verbosity, voice. Confirm we'll respect `voice-quality.md` (or note any deviation with rationale).
10. **Authority boundaries** — what it can decide alone, what routes to me, what it never modifies.

After we've worked through all 10 sections, write the persona file at `workspace/personas/per_{name}.md` where `{name}` is a lowercase slug derived from the persona name.

**Critical:** the new file's frontmatter must include `atlas_tier: user` (NOT `framework`). The template ships as framework content; the personas I create are mine. Atlas will leave `user`-tier files alone on upgrade.

After writing the file, tell me:

1. The path of the new persona file
2. A one-sentence summary of what it does
3. The activation trigger (so I know how to invoke it)
4. The wiring instructions for my AI tool (Claude Code → reference in `.claude/CLAUDE.md`; Cursor → reference in `.cursorrules`; Codex → reference in `AGENTS.md`)

If any section is genuinely incomplete or vague, surface that and let me decide whether to ship anyway or iterate. Don't fill in placeholder text just to make the file look complete.

---

*Open Atlas governs how your AI assistant behaves inside your workspace. If you need to govern how your organization adopts AI more broadly — acceptable use, risk tiers, use-case review, board reporting — that's a different problem at a different layer. See [shelbycanyon.com](https://shelbycanyon.com) for that kind of work.*

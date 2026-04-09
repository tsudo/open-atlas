---
atlas_tier: framework
title: "capture"
doc_type: skill
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [skill, capture, structured-extraction, first-skill]
function: workflow
hotwords: [capture, capture this, save this, drop this]
---

# Skill: capture

**Function:** workflow
**Composes strategy:** [`structured-extraction`](../templates/strategy/structured-extraction.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `capture`, `capture this`, `save this`, `drop this`

---

## Purpose

Take raw input from the user (a paste, a brain dump, a thought, a transcript chunk, an idea) and turn it into a structured draft saved to the right place in the workspace. The "I have a thought, where does it go?" skill — the first skill most users invoke and the highest-frequency skill in the workspace.

---

## Procedure

1. **Read the raw input.** Whatever the user pasted or said. Treat it as the source of truth for content.

2. **Compose the [`structured-extraction`](../templates/strategy/structured-extraction.md) strategy.** Its job is to pull structure from unstructured input without inferring or embellishing. Specifically:
   - Identify what's in the input: facts, decisions, actions, ideas, observations
   - Classify items as `confirmed` (explicit), `inferred` (reasonable conclusion), or `unknown` (referenced but unclear)
   - Do not add content the source doesn't support

3. **Classify the artifact type** by matching the input against these categories. Pick the closest fit. Do not invent new categories.
   - `note` — a thought, observation, or idea worth keeping
   - `decision` — a choice with rationale (or in-progress thinking toward one)
   - `action` — something to do, with owner if named
   - `transcript-fragment` — captured conversation or meeting content
   - `reference` — a link, quote, or external source worth keeping
   - `mixed` — the input contains more than one type; the output preserves them as separate sections

4. **Pick the destination directory** based on the classification and what exists in the workspace. Default to `workspace/drafts/` if you're not sure — promotion is a human action, capture is just the entry point. Specifically:
   - `note`, `mixed`, `transcript-fragment` → `workspace/drafts/`
   - `decision` → `workspace/drafts/` (do NOT route to `workspace/knowledge/decisions/` automatically — that's a promotion the human makes after review)
   - `action` → `workspace/drafts/` with a clear `## Action` section the user can copy into their task system
   - `reference` → `workspace/drafts/` (or, if the user explicitly named a topic directory under `workspace/knowledge/[topic]/`, route there)

5. **Generate a filename.** Lowercase, hyphen-separated, derived from the input's first sentence or stated topic. Maximum ~60 characters before the `.md` extension. If a file with that name already exists, append a date suffix.

6. **Write the file** to the chosen destination with frontmatter that conforms to the workspace's frontmatter schema (`title`, `doc_type`, `status: draft`, `created`, `updated`, `owner`, `tags`). Use today's date for `created` and `updated`. Pull `owner` from `workspace/context/human-operating-model.md` if available, otherwise omit and let the user fill it in.

7. **Return the output in the [base contract](../templates/output-contract/base.md) shape with the body sections defined below.**

---

## What This Skill Does NOT Do

- Does NOT promote captured content beyond `status: draft`. Promotion to `reviewed` or `canonical` is a human action.
- Does NOT make routing decisions outside of `workspace/drafts/` unless the user explicitly named a destination. Guessing wrong is worse than defaulting to drafts.
- Does NOT edit existing files. Only creates new ones. If a name collision happens, append a date suffix; do not overwrite.
- Does NOT modify templates. Only consumes them.
- Does NOT classify content the user explicitly tells you to leave uncategorized — set `doc_type: note` and move on.
- Does NOT extract content the source does not support. No inference, no summarizing, no embellishment.

---

## Inputs

- **Required:** Raw text content. Any length. Any format. A sentence, a paragraph, a paste, a transcript chunk.
- **Optional:** A hint about the artifact type ("this is a decision," "this is a meeting note") — overrides automatic classification.
- **Optional:** A target filename — overrides auto-generated name.
- **Optional:** A target directory — overrides default routing.

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections** (in this order):

- `## Captured` — one-sentence summary of what was captured ("Captured a decision-in-progress about database selection")
- `## File Created` — the absolute path to the new file, plus the chosen `doc_type` and `status`
- `## Classification` — the artifact type that was picked + a one-sentence "why this category" reasoning
- `## Routing` — the destination directory and a one-sentence "why here" reasoning

**Status mapping:**

- `DONE` — file written successfully with valid frontmatter, classification was unambiguous, routing was clear
- `DONE_WITH_NOTES` — file written but classification was ambiguous (multiple categories could fit) or routing required a fallback to `workspace/drafts/`. Notes section explains.
- `BLOCKED` — could not write the file (target directory doesn't exist, filesystem error, name collision that can't be resolved with a date suffix)
- `NEEDS_CONTEXT` — input is too vague or empty to classify; Notes section names what's needed

---

## Activation

- **Hotword(s):** `capture`, `capture this`, `save this`, `drop this`
- **Explicit invocation:** `Read workspace/skills/capture.md and capture this: [paste]`
- **Tool wiring:** Claude Code slash command `/capture`; Cursor / Codex / generic — reference this file in the tool's context configuration

---

## Resources

- [`workspace/templates/strategy/structured-extraction.md`](../templates/strategy/structured-extraction.md) — the reasoning strategy this skill composes
- [`workspace/templates/output-contract/base.md`](../templates/output-contract/base.md) — the output envelope this skill composes
- [`workspace/templates/schema/`](../templates/schema/) — frontmatter conventions the file must conform to
- [`workspace/skills/extract-knowledge.md`](extract-knowledge.md) — invoke separately when the captured content has durable insight worth promoting toward `workspace/knowledge/`
- [`workspace/skills/think-it-through.md`](think-it-through.md) — invoke separately when the captured content is a decision the user is stuck on

---

*Workflow skill composing `structured-extraction` + base output contract. The canonical first skill — high-frequency, low-discipline, default-to-drafts. The "where does this go?" entry point for the workspace.*

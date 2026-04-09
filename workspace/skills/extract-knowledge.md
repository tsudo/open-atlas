---
atlas_tier: framework
title: "extract-knowledge"
doc_type: skill
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [skill, knowledge, structured-extraction, durable-insight]
function: workflow
hotwords: [extract knowledge, save what I learned, knowledge it, durable insight]
---

# Skill: extract-knowledge

**Function:** workflow
**Composes strategy:** [`structured-extraction`](../templates/strategy/structured-extraction.md)
**Composes contract:** [base output contract](../templates/output-contract/base.md)
**Hotwords:** `extract knowledge`, `save what I learned`, `knowledge it`, `durable insight`

---

## Purpose

Take a piece of content (a conversation, a draft, a paste, the user's stated insight) and turn it into a durable knowledge artifact saved to `workspace/knowledge/`. The "I learned something today, capture it before I lose it" skill. The compounding move that turns a one-time insight into a workspace asset.

---

## Procedure

1. **Read the input.** Treat it as the source. Do not add content the source does not support.

2. **Apply the durability rubric.** A piece of content is *durable knowledge* (vs. ephemeral capture) if it meets at least two of these four criteria:
   - **Reusable** — the user (or future-user) will want this insight again, in a different situation
   - **Generalizable** — the insight applies beyond the specific situation that produced it
   - **Costly to re-derive** — recovering this insight from scratch would take meaningful effort
   - **Independent of context** — the insight stands on its own without needing the originating conversation

   If the content meets fewer than two criteria, it is NOT durable knowledge. Set status to `BLOCKED` with a note explaining which criteria failed and recommending the user invoke `capture` instead.

3. **Compose the [`structured-extraction`](../templates/strategy/structured-extraction.md) strategy** to pull the durable insight from the surrounding noise:
   - Identify the central claim or pattern (the thesis)
   - Identify the supporting reasoning (the why)
   - Identify the conditions or scope (the when-it-applies)
   - Identify the sources or evidence (the receipts)
   - Mark items as `confirmed` (explicit in source), `inferred` (reasonable conclusion), or `unknown` (referenced but insufficient detail)

4. **Pick a topic directory.** The artifact lands at `workspace/knowledge/[topic]/[slug].md`. Topic selection rules:
   - If `workspace/knowledge/` already has subdirectories, pick the closest match
   - If no match exists, ask the user what topic to use — do not invent a new topic without confirmation
   - If the user confirms a new topic, create the directory as part of writing the file

5. **Generate a filename.** Lowercase, hyphen-separated, derived from the thesis. Maximum ~60 characters before the `.md` extension. Append a date suffix if a collision exists.

6. **Write the file** with this body structure (lightweight, KNO-schema-independent — promotion to a fully-formed KNO is a future user action):
   - `## Thesis` — one or two sentences. The central insight.
   - `## Core Guidance` — what to know, when it applies, what to do with it
   - `## Sources` — where the insight came from (conversation date, draft path, external link)
   - `## Open Questions` — anything the source raised but didn't resolve (optional, omit if none)

   Frontmatter: `title`, `doc_type: knowledge`, `status: draft`, `created`, `updated`, `owner`, `tags`. Use today's date.

7. **Return the output in the [base contract](../templates/output-contract/base.md) shape with the body sections defined below.**

---

## What This Skill Does NOT Do

- Does NOT promote the artifact beyond `status: draft`. Promotion to `reviewed` or `canonical` is a human action after the user reads what was extracted.
- Does NOT enforce a strict KNO schema. v1.1 ships a lightweight knowledge format. Users who want stricter KNO discipline can edit the file post-creation; the skill produces the artifact, the user shapes it.
- Does NOT extract insights the source does not support. No inference beyond what's marked `inferred`. No fabrication. No "the user probably meant..."
- Does NOT capture ephemeral content. If the durability rubric fails, route the user to `capture` instead.
- Does NOT modify existing knowledge files. Only creates new ones.
- Does NOT decide topic directories without confirmation. New topics require the user to name them.
- Does NOT replace `capture`. Capture is for raw content that needs a home; extract-knowledge is for already-distilled insight that needs a permanent place.

---

## Inputs

- **Required:** Content the user wants extracted as knowledge. Can be a conversation excerpt, a draft, a paste, or the user's stated insight in their own words.
- **Optional:** A topic directory the user wants to use (overrides automatic topic selection).
- **Optional:** A target filename — overrides auto-generated name.
- **Optional:** A confidence assessment from the user (how sure are they about this insight).

---

## Output Contract

**Composes:** [base output contract](../templates/output-contract/base.md)

**Required body sections** (in this order):

- `## Extracted` — the thesis in one sentence (the headline insight)
- `## File Created` — the absolute path to the new file, plus `doc_type: knowledge` and `status: draft`
- `## Topic` — the topic directory chosen + a one-sentence "why this topic" reasoning
- `## Durability Check` — which two-or-more rubric criteria the content met (reusable / generalizable / costly-to-re-derive / context-independent)
- `## Open Questions` — any unresolved questions surfaced during extraction, or "none" if resolved

**Status mapping:**

- `DONE` — file written successfully with valid frontmatter, durability rubric passed, topic was clear (existing or user-confirmed)
- `DONE_WITH_NOTES` — file written but with a caveat (e.g., topic was a new directory the user just created, or the extraction had partial inference). Notes section explains.
- `BLOCKED` — durability rubric failed (content does not meet two-or-more criteria). Notes section names which criteria failed and recommends `capture` instead.
- `NEEDS_CONTEXT` — content is too vague to extract a thesis, or topic is ambiguous and user has not confirmed. Notes section names the specific clarification needed.

---

## Activation

- **Hotword(s):** `extract knowledge`, `save what I learned`, `knowledge it`, `durable insight`
- **Explicit invocation:** `Read workspace/skills/extract-knowledge.md and extract knowledge from this: [paste]`
- **Tool wiring:** Claude Code slash command `/extract-knowledge`; Cursor / Codex / generic — reference this file in the tool's context configuration

---

## Resources

- [`workspace/templates/strategy/structured-extraction.md`](../templates/strategy/structured-extraction.md) — the reasoning strategy this skill composes
- [`workspace/templates/output-contract/base.md`](../templates/output-contract/base.md) — the output envelope this skill composes
- [`workspace/templates/schema/`](../templates/schema/) — frontmatter conventions the file must conform to
- [`workspace/skills/capture.md`](capture.md) — invoke instead when content is ephemeral, not durable
- [`workspace/skills/review-context.md`](review-context.md) — invoke periodically to surface knowledge files that need promotion or archival

---

*Workflow skill composing `structured-extraction` + base output contract, with a durability rubric that distinguishes reusable insight from ephemeral capture. KNO-independent — produces a lightweight knowledge artifact the user can promote to a stricter format later.*

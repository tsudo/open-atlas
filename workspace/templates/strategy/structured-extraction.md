---
atlas_tier: framework
title: "Strategy: Structured Extraction"
doc_type: strategy-template
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [strategy, structured-extraction, extraction]
---

# Strategy: Structured Extraction

**When to use:** Pulling structured data from unstructured input — notes, transcripts, chat logs, documents. Default strategy for the `capture` and `extract-knowledge` skills.

**When NOT to use:** Creative tasks, open-ended analysis, drafting.

---

## Instruction

1. Identify what must be extracted (facts, decisions, entities, action items, preferences)
2. Extract only what the source explicitly supports — do not infer, summarize, or embellish
3. Classify each extracted item:
   - **Confirmed** — explicitly stated in the source
   - **Inferred** — reasonable conclusion but not explicit
   - **Unknown** — referenced but insufficient detail
4. Do not repeat items across categories
5. Do not start items with the same opening words — vary phrasing
6. Preserve original language for quotes and proper nouns
7. Flag ambiguities rather than resolving them silently

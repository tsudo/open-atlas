# Cross-Session Learnings

> **Note on naming:** This page is about the *system's* learning loop — how your workspace captures execution failures, surprising wins, and recurring patterns over time so future sessions don't re-discover the same lessons. That is distinct from the *training/* folder, which is human pedagogy (the documents you read to learn the framework). Two different concepts: "learning" = system feedback, "training" = human onboarding. Don't conflate them.
>
> **Relationship to [`feedback-loop.md`](feedback-loop.md):** This page describes the *pattern* — a flat append-only markdown log of discoveries — at its simplest. [`feedback-loop.md`](feedback-loop.md) describes Open Atlas's *fuller implementation* of the same idea using POSITIVE_SIGNAL events (a structured JSONL format that gate and steward skills emit automatically). Both pages cover the same conceptual territory from different altitudes. **Use this page if you want the pattern without infrastructure**; use feedback-loop.md if you want to know how Open Atlas's production skills wire it in.

## The Problem

AI tools start fresh every session. The context window resets, and everything discovered yesterday — the workaround for that platform bug, the correct invocation for that CLI tool, the reason you structured the database that way — is gone.

You end up re-explaining the same pitfalls. The AI makes the same mistake, you correct it, and next session the cycle repeats. Session transcripts technically contain this knowledge, but they're too long and unstructured to search effectively.

## The Pattern

Maintain an append-only learnings file. Each entry captures a single discovery: what was learned, how confident you are, where it came from, and what it affects.

The file is deliberately simple. It's not a knowledge base, a wiki, or a database. It's a flat list of things that surprised you or will matter next time.

## Suggested Schema

Start with plain markdown. Each entry gets a sequential ID, a short slug, and a date.

```markdown
## L-0001 — py-not-python (2026-03-14)
**Type:** tool | **Confidence:** 10/10
On Windows with Git Bash, use 'py' not 'python'. The Windows Store stub intercepts 'python'.
**Files:** context/local-dev-environment.md
**Tags:** python, windows, tooling
```

```markdown
## L-0002 — wrangler-deploy-not-publish (2026-03-18)
**Type:** tool | **Confidence:** 10/10
Wrangler v3 uses 'wrangler deploy', not 'wrangler publish'. The old command still works but is deprecated and will be removed.
**Files:** skills/sk_publish-blog.md
**Tags:** cloudflare, wrangler, deployment
```

```markdown
## L-0003 — jq-empty-string-truthy (2026-03-22)
**Type:** code | **Confidence:** 8/10
In jq, empty string "" is truthy. Use 'length > 0' or '// empty' to filter blank values, not just truthiness checks.
**Files:** tools/hooks/protect-governance.sh
**Tags:** jq, bash, gotcha
```

Fields:

- **Type** — `tool`, `code`, `process`, `platform`, or `decision`
- **Confidence** — 1-10 scale. How sure are you this is correct? Low-confidence learnings should be verified before relying on them.
- **Body** — One to three sentences. What happened, why it matters, and what to do instead.
- **Files** — Which files this learning affects. Helps with targeted search.
- **Tags** — Freeform. Used for grep-based retrieval.

## Search Before Work

Before starting a task, grep your learnings file for relevant keywords. This is the single most important habit for making learnings useful.

```bash
grep -i "wrangler\|deploy\|cloudflare" learnings.md
```

If your AI tool supports a search-before-work instruction, wire it in. The cost is a few seconds per session start. The benefit is never repeating a solved problem.

## Write at Completion

After completing work, ask yourself: did anything surprise me? Did I discover a workaround, a constraint, or a behavior that isn't documented elsewhere?

If yes, append an entry. If no, don't. Not every session produces learnings. Forcing an entry when nothing was learned creates noise that degrades search quality.

Good signals that something is worth capturing:

- You had to try multiple approaches before one worked
- A tool behaved differently than its documentation suggested
- You discovered a constraint that will affect future work
- You made a decision that future-you will need to remember the reasoning for

## Relationship to Decisions

Learnings and decisions are related but distinct:

- **Learnings** are what you discovered. "The Windows Store stub intercepts `python`."
- **Decisions** are what you chose. "We will use `py` as the Python invocation in all scripts."

Both persist across sessions. Both prevent re-work. But they serve different purposes: learnings capture ground truth, decisions capture intent. A decision log answers "why did we do it this way?" A learnings file answers "what do we know that isn't obvious?"

## Why Learnings Replaced Session Logs

Earlier patterns tried to capture cross-session knowledge through full session logs (every interaction recorded for later review). This approach has a failure mode: the discipline of writing comprehensive session logs is too high to maintain, so the logs either become noise or stop happening entirely. Learnings fix this by inverting the contract — instead of logging everything in case it matters, you log only what surprised you. The signal density is dramatically higher, and the discipline is sustainable.

Your AI tool's session transcripts already capture the full conversation. You don't need to duplicate that. What you need is the *delta* — the things that aren't in the documentation, that aren't obvious from the code, that future-you will wish you had written down.

---

*Start with a simple markdown file. Graduate to JSONL when you have enough entries to need structured search.*

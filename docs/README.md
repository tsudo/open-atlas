# Open Atlas Docs

Three layers, by intent.

## Day 1 — Get Started

You just installed Open Atlas. You want to understand what you've got and run your first session.

- **[day-1/first-session.md](day-1/first-session.md)** — Walk through your first conversation: what to say, what to expect, what to do when something feels off.
- **[day-1/core-concepts.md](day-1/core-concepts.md)** — The five concepts you need (workspace, persona, skill, hook, manifest) and the minimum-viable-adoption path that uses only two of them.

## Day 2 — Build Your Own

You've used Open Atlas for a session or two. Now you want to make it yours.

- **[day-2/creating-personas.md](day-2/creating-personas.md)** — When to add a persona, how to design one, how to test it.
- **[day-2/creating-skills.md](day-2/creating-skills.md)** — When to add a skill, the four function classes, how to wire it.
- **[day-2/using-hooks.md](day-2/using-hooks.md)** — When (and whether) to add hooks. Includes a "is this for you yet?" preflight.
- **[day-2/workspace-maintenance.md](day-2/workspace-maintenance.md)** — Drafts piling up, frontmatter drift, audit cadence, when to archive.

## Reference

You're looking up a specific concept, contract, or decision.

- **[reference/anti-patterns.md](reference/anti-patterns.md)** — What good output looks like, what bad output looks like, the disciplines that separate them.
- **[reference/feedback-loop.md](reference/feedback-loop.md)** — POSITIVE_SIGNAL events, learning vs training, how the workspace learns from itself.
- **[reference/learnings.md](reference/learnings.md)** — The learnings-file pattern: a simple append-only markdown log for cross-session discoveries. The portable, low-discipline pattern that pairs with the feedback-loop concept above.
- **[reference/model-tiers.md](reference/model-tiers.md)** — Multi-model template routing: declaring intent-level model tiers in templates so the AI tool resolves them to a specific model at runtime.
- **[reference/skill-functions.md](reference/skill-functions.md)** — The four function classes (gate / steward / workflow / utility) in depth.
- **[reference/output-contracts.md](reference/output-contracts.md)** — Forced verdicts, completion status, evidence requirements.
- **[reference/manifest.md](reference/manifest.md)** — The three-tier file ownership model (core / framework / user) and how `.atlas-manifest.yml` works.
- **[reference/compatibility.md](reference/compatibility.md)** — What works where (Claude Code, Cursor, Codex, generic). Honest table.
- **[reference/hooks.md](reference/hooks.md)** — Hooks reference (v1.1 ships designs; working scripts in v1.2).
- **[reference/why-this-works.md](reference/why-this-works.md)** — The design rationale behind Open Atlas. Read this when you want to understand *why* the conventions are the way they are.

---

## How to read these docs

- **Just installed?** Day 1, in order. Skip everything else.
- **Building for a team?** Day 1 → Day 2 → the relevant reference docs.
- **Adapting Open Atlas to something opinionated?** Read [reference/why-this-works.md](reference/why-this-works.md) first so you know what you're trading away.

You do not need all of this. The minimum-viable-adoption path in [day-1/core-concepts.md](day-1/core-concepts.md) gets you a working Open Atlas with two concepts and zero hooks.

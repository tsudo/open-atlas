---
title: Anti-Patterns for AI Workspaces
doc_type: reference
status: reviewed
created: 2026-03-31
updated: 2026-04-07
owner: open-atlas
tags: [reference, anti-patterns, voice, workspace, discipline]
atlas_tier: framework
---

# Anti-Patterns for AI Workspaces

Common failure modes in AI-assisted workspaces. Recognizing these early prevents compounding problems across sessions.

## Workspace Anti-Patterns

**Flat folder structures.** Everything in one directory with no hierarchy. The AI has no spatial cues about what relates to what, and neither do you. Organize by function or domain, not by date.

**No metadata.** Files without frontmatter, headers, or any machine-readable context. The AI has to read the entire file to understand what it is. Even a three-line YAML header (type, status, date) dramatically improves retrieval and routing.

**Mixing drafts with finals.** Draft documents sitting alongside published or canonical files with no way to distinguish them. Use status fields, naming conventions, or separate directories. The AI should never have to guess whether a file is authoritative.

**No index or blueprint file.** A workspace with dozens of files and no map. A single `CLAUDE.md`, `README.md`, or blueprint file at the root that describes the directory structure saves the AI from exploratory searches every session.

**No naming convention.** Files named `notes.md`, `stuff.txt`, `doc1.md`. Inconsistent naming forces the AI to open files to understand them. A convention like `{topic}-{type}-{date}.md` makes the filesystem self-documenting.

**Over-structuring low-value work.** Not every note deserves durability. Open Atlas gives you templates, schemas, and lifecycle states because *some* work earns the overhead. Most work doesn't. If you're filling out frontmatter for a three-line shopping list, or running a one-off thought through `draft → reviewed → canonical`, you're over-structuring. The discipline is asking "will I read this six months from now?" before promoting anything to a knowledge object.

| BAD | GOOD |
|-----|------|
| Every brain-dump becomes a knowledge object with full frontmatter, lifecycle, and tags | Brain-dumps go in `drafts/` with minimal metadata; only the ideas that survive a week get promoted |
| Every meeting produces a `meeting-notes.md` file with structured templates, even for 5-minute syncs | Meeting templates are reserved for meetings whose decisions or actions matter beyond the day |

**Cargo-cult vocabulary.** Adopting the framework's words without adopting its workflows. People learn what "knowledge object" or "human operating model" means as nouns, but they don't change how they work with AI. The framework's value is in the workflows, not the vocabulary. If you're collecting terms without changing your habits, you're cargo-culting.

| BAD | GOOD |
|-----|------|
| "I created a human operating model file but I never reference it in conversations with my AI" | "I keep my human operating model file open and add to it whenever I notice my AI making the wrong assumptions about how I work" |
| "I wrote a knowledge object about caching patterns, then never linked to it from any other document" | "When I ran into the caching question again, I grepped for 'caching' first, found my prior KNO, and built on it" |

## Voice Anti-Patterns

**Sycophancy.** The AI agrees with everything and validates before answering. This wastes tokens and erodes trust.

| BAD | GOOD |
|-----|------|
| "Great question! That's a really insightful observation." | "The answer is X, because Y." |
| "You're absolutely right that..." | "That's partially correct. The nuance is..." |
| "I'd be happy to help with that!" | *(just help)* |

**No pushback.** The AI never disagrees, flags risks, or suggests alternatives. A well-configured AI should push back when it sees problems — not obstruct, but surface concerns before proceeding.

**Banned vocabulary.** These terms almost always signal filler rather than substance. Train your AI to avoid them:

- "delve," "dive into," "deep dive"
- "leverage," "utilize" (use "use")
- "robust," "seamless," "cutting-edge"
- "synergy," "holistic," "paradigm"
- "empower," "revolutionize," "transform"
- "It's important to note that..."
- "In today's rapidly evolving..."
- "Let me unpack that"
- "At the end of the day"
- "Moving forward"

If the AI needs twenty words of throat-clearing before answering, the throat-clearing is the problem.

## Review and Skill Anti-Patterns

When skills review work or produce verdicts, hedged language defeats the purpose. A review that says "this looks mostly fine, you might want to check X" gives no signal. A review that says "PASS, except line 47 has a credential leak — fix before ship" is useful.

**Hedged verdicts.** Reviews that don't commit to an outcome.

| BAD | GOOD |
|-----|------|
| "This seems okay but I'd consider double-checking the auth flow" | "PASS-WITH-NOTES. Auth flow at `src/auth.ts:42` requires manual verification before ship — see PROOF below" |
| "Probably no secrets but it might be worth scanning" | "PASS. Scanned for OpenAI keys, AWS credentials, GitHub tokens, generic Bearer patterns. No matches" |
| "This is mostly good" | "BLOCK. Three findings, all confirmed: (1) secret at `config.yml:12`, (2) absolute path at `deploy.sh:8`, (3) PII in `test-data.json`" |

**No PROOF requirement.** Findings without evidence — "I think there's a problem somewhere in the code" — are noise. Every finding should cite a specific file, line number, and the offending content.

**Anti-skip clause.** Skills should never silently skip their own gates. If a skill cannot run a check (because the file is missing, the tool isn't available, or the input is malformed), the skill must emit a `SKIPPED` status with the reason — not pretend the check passed. A clean run with no signal is indistinguishable from a broken run with no signal; the only way to tell them apart is to make skipping explicit.

**Forced verdicts.** Gate-class skills (publish checks, security reviews, code reviews) must emit one of a fixed set of values: `PASS`, `PASS-WITH-NOTES`, `BLOCK`, `SKIPPED`. No "looks good," no "probably fine," no "seems okay." Forced vocabularies prevent the gradual drift toward hedged language.

## Output Anti-Patterns

AI-generated content has recognizable tells. Catching these before publication is a quality gate, not an aesthetic preference.

**Purple gradients.** Hero sections with gradient backgrounds (especially purple-to-blue) that look like every other AI-generated landing page. If your site looks like a ChatGPT demo, it signals that no human made design decisions.

**Three-column feature grids.** The default AI layout: three cards in a row, each with an icon, a heading, and a paragraph. Occasionally useful, almost always a sign that no one thought about information hierarchy.

**Emoji as design.** Section headers decorated with emoji instead of actual visual design. Emoji in documentation is fine; emoji as a substitute for typography and layout is not.

**ChatGPT bold-keyword lists.** Paragraphs where every other phrase is **bolded** to simulate emphasis. Real emphasis is selective. If everything is emphasized, nothing is.

**Restating the question.** "You asked about X. X is a topic that..." Just answer. The user knows what they asked.

**"In conclusion" summaries.** Repeating everything that was just said in a summary paragraph. If the document is well-structured, the reader doesn't need a recap at the end.

**Generic stock imagery descriptions.** "Imagine a team collaborating around a whiteboard..." If you're describing an image instead of showing one, cut the description.

---

*AI slop detection inspired by gstack (Garry Tan, MIT license). Adapted and extended.*

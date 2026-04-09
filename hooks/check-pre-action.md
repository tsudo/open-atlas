---
atlas_tier: framework
title: check-pre-action — Explainer
doc_type: doc
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [hooks, pre-action, conventions, explainer]
---

# check-pre-action — Explainer

> **v1.1 status: design notes only.** This file is the design specification for a hook Open Atlas plans to ship as a working script in v1.2. The patterns, postures, and customization guidance below are the design contract. If you want to implement this hook now in your own scripting language, this file tells you what to enforce and why. See [README.md](README.md) for the v1.1 → v1.2 framing.

---

## What it does

Catches the convention slip-ups a fast-moving AI assistant makes when it skips the boring parts:

1. **Plan output location** — flags when the model writes a "plan" or "analysis" file to a random location instead of the project's holding area.
2. **Template-load reminder** — when the model creates a new KNO, persona, or skill, reminds it (and you) to confirm the governing template is loaded.
3. **Pre-build discipline** — when the model creates a new framework-tier file (governance docs, top-level templates), prompts a "did you red-team this?" pause.

This hook **flags, it does not block.** All three checks are conventions, not security violations. The point is to make the slip-ups visible so you can correct them, not to make them impossible.

---

## Why this hook exists

These three checks each came from a real incident in the workspace this hook was derived from. Each one is the codified form of a feedback memory — a moment where the model did the wrong thing once, the user explained why, and the convention got encoded so it wouldn't happen again.

If you find yourself wishing this hook caught a *fourth* class of slip-up, that's exactly the right instinct. Add a check.

---

## What each check fires on

### Check 1: Plan output location

**Triggers when:** the file path matches `*/plans/*`, `*/plan-*.md`, or `*-plan.md` AND it's not under `workspace/drafts/` or `workspace/projects/`.

**Why:** plans tend to scatter. Some land in `.claude/plans/`, some in random directories, some next to the file they're planning. Once that happens you can never find the plan again. Pinning plans to one of two predictable locations (drafts for unaffiliated plans, projects for project-affiliated plans) keeps them recoverable.

**Customize:** edit the second `grep -qE` clause to match your workspace's holding-area paths.

### Check 2: Template-load reminder

**Triggers when:** the file is *new* (does not exist yet) AND the path matches a known durable-artifact location (`workspace/knowledge/*.md`, `workspace/personas/per_*.md`, `workspace/skills/*.md`).

**Why:** durable artifacts have governing templates for a reason — required frontmatter, required sections, conventions about voice and structure. The model is fully capable of writing one of these from scratch and getting all of those things subtly wrong. The hook can't verify the template was *actually* loaded; it can only remind. The reminder is enough to catch the freestyling case.

**Customize:** add new `case` clauses for any other durable artifact type your workspace uses.

### Check 3: Pre-build discipline

**Triggers when:** the file is *new* AND the path is in framework-tier infrastructure (governance docs, top-level templates).

**Why:** new framework files should not be casual additions. They earn their place through deliberation — a red-team, an ADR, or a planning doc. The hook surfaces the moment the model is about to add one, so you can confirm the deliberation happened.

**Customize:** edit the path patterns in the `case` clause to match what counts as "load-bearing infrastructure" in your workspace.

---

## When it fires, what to do

1. **Read the warning.** Each warning starts with `PRE-ACTION WARN [rule-name]:` so you can see immediately which check tripped.
2. **Decide:**
   - **The model is about to do the wrong thing** → cancel the operation, fix the path or load the template, and retry.
   - **The model is doing the right thing in an unusual way** → tighten the check so future runs don't false-positive on the same legitimate pattern.
   - **The check is wrong for your workspace** → edit the script. These are your conventions, not Open Atlas's.

---

## Why this hook does not block

Conventions are not security boundaries. A misplaced plan file is annoying, not dangerous. Blocking would generate friction; flagging generates an audit trail you can review.

For checks where blocking *is* the right call (credential leaks), see `check-credential-leak.md`.

---

## Customizing it for your workspace

This is the most workspace-specific of the three Open Atlas hooks. Expect to edit it within your first week of use:

- **Add a new convention check** → add a section after the existing three. Each section should:
  - Document what it catches and why (comment block)
  - Match against `$NORM_PATH` (forward-slash normalized)
  - Call `warn "rule-name" "human-readable message"` on a hit
- **Tighten a path pattern** → most patterns use simple `case` globs or `grep -qE` regexes; edit in place
- **Change the warning prefix** → edit the `warn()` function

---

## Resources

- `hooks/check-pre-action.sh` — the script
- `hooks/README.md` — hooks orientation and wiring
- `docs/day-2/using-hooks.md` — broader hooks workflow
- `docs/reference/anti-patterns.md` — the discipline these checks codify

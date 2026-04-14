---
atlas_tier: framework
title: "oa-update"
doc_type: skill
status: reviewed
created: 2026-04-13
updated: 2026-04-13
owner: open-atlas
tags: [skill, update, upstream, version-check, maintenance]
function: utility
hotwords: [check for updates, oa update, what's new in atlas, update check]
freedom_level: high
---

# Skill: oa-update

**Function:** utility
**Freedom level:** high
**Hotwords:** `check for updates`, `oa update`, `what's new in atlas`, `update check`

---

## Purpose

Check for upstream Open Atlas releases and relevant AI tool documentation changes. Compares the user's current workspace version against the latest published release and surfaces what changed, what's new, and what the user might want to adopt. Read-only — surfaces recommendations, never auto-upgrades.

---

## What This Skill Does

- Reads the local `.atlas-manifest.yml` to determine the current workspace version
- Checks the Open Atlas GitHub repository for newer releases
- Checks official Anthropic and OpenAI Codex documentation for features that affect workspace governance
- Produces a categorized update summary with recommended actions
- Reports gracefully when sources are unavailable

---

## What This Skill Does NOT Do

- Auto-upgrade workspace files — the user decides what to adopt
- Check sources beyond the Open Atlas repo and official AI tool docs
- Modify any files in the workspace
- Track update history across sessions (stateless — each run is fresh)
- Replace reading release notes — this is a summary, not a substitute

---

## Inputs

- **Required:** None (reads local manifest automatically)
- **Optional:** A specific area of interest ("what's new in skills?", "any security updates?")

---

## Procedure

### 1. Read local version

Read `.atlas-manifest.yml` from the workspace root. Extract:
- `atlas_version` — the current Open Atlas version
- `generated` — when the manifest was last generated

If the manifest is missing or has no `atlas_version`, report: "Can't determine your current version. Check that `.atlas-manifest.yml` exists at the workspace root and has an `atlas_version` field."

### 2. Check Open Atlas repo

Fetch the latest release information from the Open Atlas GitHub repository (github.com/tsudo/open-atlas). Look for:
- Latest release tag and date
- Release notes or changelog
- New files added since the user's version
- Changed files (framework-tier) since the user's version
- Any breaking changes or migration notes

**If fetch fails** (no internet, rate limited, repository unavailable): report "Couldn't reach the Open Atlas repository. Skipping upstream version check." Continue to step 3.

### 3. Check AI tool documentation

Fetch current documentation from official sources:
- **Anthropic Claude Code** — skill specification, hook capabilities, context loading patterns, new features
- **OpenAI Codex** — skill/agent specification, configuration patterns, new features

Look for features that affect workspace governance:
- New skill metadata fields
- New hook event types or capabilities
- Changes to context loading behavior
- New file conventions or configuration patterns

**If fetch fails:** report which source couldn't be reached. Continue with whatever succeeded.

### 4. Produce update summary

Organize findings into three categories:

**a) Open Atlas framework updates**
- Version comparison (current vs latest)
- New files added (list by manifest tier: framework vs user)
- Changed framework files (what changed and why it matters)
- Breaking changes (if any)
- Migration notes (if any)

**b) AI tool features to consider**
- New capabilities your workspace could use
- Changes that affect existing workspace patterns
- Deprecations that might require updates

**c) Recommended actions**
- Prioritized list of what the user should consider doing
- For each action: what to do, why it matters, estimated effort (S/M/L)

### 5. Present summary

Present the update summary. If the user's workspace is current and no relevant AI tool changes were found, say so: "Your workspace is on [version], which is the latest. No relevant AI tool changes found."

Do not pad the output with filler when there's nothing to report.

---

## Output Contract

**Required body sections:**

- `## Version Status` — current vs latest, with date comparison
- `## Updates Available` — categorized findings (or "none" if current)
- `## Recommended Actions` — prioritized list (or "none" if current)

**Status mapping:**

- `DONE` — all sources checked, summary produced
- `DONE_WITH_NOTES` — some sources unreachable, partial summary produced
- `BLOCKED` — no sources reachable and local manifest unreadable
- `NEEDS_CONTEXT` — n/a (this skill always has enough context to attempt a check)

---

## Authority Boundaries

- Can read `.atlas-manifest.yml` and any workspace file for version context
- Can fetch from github.com/tsudo/open-atlas (releases, changelog, file listings)
- Can fetch from official Anthropic and OpenAI documentation
- Cannot write to any workspace file
- Cannot modify the manifest
- Cannot auto-apply updates

---

## Resources

- `.atlas-manifest.yml` — local version anchor
- `docs/reference/manifest.md` — how the manifest works
- `workspace/BLUEPRINT.md` — workspace structure reference

---

*Created from `workspace/templates/skill/skill-template.md` (Open Atlas v1.2).*

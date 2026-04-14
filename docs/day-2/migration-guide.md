# Migration Guide

How to adopt Open Atlas when you already have an AI workspace — files, conventions, maybe even governance.

---

## Before you start

Migration is a series of decisions, not a script. The biggest mistakes happen when you start copying files before deciding what you want to keep, what you want to replace, and what you want to layer.

Answer these questions first:

1. **What do you already have?** List your existing AI workspace files — instruction files, memory/learnings, personas, skills, templates, governance docs.
2. **What's working?** Which conventions do you want to keep? Which rules are battle-tested and should survive the migration?
3. **What's not working?** What prompted you to look at Atlas? The answer tells you where to focus.
4. **Layer or replace?** Are you adopting Atlas as your primary framework (replace), or layering it under your existing governance (layer)? See [governance layering](../reference/governance-layering.md) for the layering model.

---

## The migration path

### Phase 1: Backup

Copy your entire workspace to a backup location before touching anything. Not optional. If the migration goes wrong, you need to be able to get back to where you started.

### Phase 2: Map your existing structure

Before adding Atlas files, understand what you have:

| Your structure | Atlas equivalent | Decision |
|---|---|---|
| AI instruction file (CLAUDE.md, AGENTS.md, etc.) | `workspace/BLUEPRINT.md` + tool instruction file | Merge your rules into the Atlas structure, or keep yours and reference Atlas |
| Personas / role definitions | `workspace/personas/` | Keep yours, add `atlas_tier: user` frontmatter |
| Skills / workflows | `workspace/skills/` | Keep yours, add frontmatter per [skill-conventions](../../workspace/governance/skill-conventions.md) |
| Templates | `workspace/templates/` | Layer — keep yours alongside Atlas templates |
| Knowledge base / notes | `workspace/knowledge/` | Move files, add frontmatter if desired |
| Governance / conventions | `workspace/governance/` | Layer using the [governance layering](../reference/governance-layering.md) pattern |

### Phase 3: Install the Atlas skeleton

Copy the Atlas `workspace/` directory into your project. This gives you:
- `workspace/governance/` — conventions, taxonomy, pipeline contract
- `workspace/templates/` — persona, skill, doc, strategy, output-contract templates
- `workspace/personas/per_open-atlas.md` — the starter persona
- `workspace/skills/` — production skills

If you already have files in these directories, Atlas files land alongside yours. The naming convention (`atlas-*` vs `[your-org]-*`) keeps them distinct.

### Phase 4: Resolve collisions

Walk each directory where both your files and Atlas files exist:

- **Same concept, different structure:** Decide which format to use going forward. If yours is better, keep it and note the Atlas file as reference-only. If Atlas's is better, migrate your content into the Atlas structure.
- **Complementary content:** Both survive. No collision.
- **True conflict:** Your rule says X, Atlas convention says Y. Use the [governance layering](../reference/governance-layering.md) model — your explicit rules override Atlas defaults.

### Phase 5: Configure your AI tool

Point your AI tool at the migrated workspace:
- Update your instruction file to reference the Atlas BLUEPRINT and governance directory
- Register Atlas production skills (slash commands, hotword triggers, or direct file references)
- If you use the manifest (`.atlas-manifest.yml`), set `atlas_tier: user` on your files and leave Atlas files as `framework`

### Phase 6: Validate

Run a workspace review to check the migrated state:
- Invoke `oa-review` (or `review-context` for a lighter check)
- Check that cross-references resolve — migration often breaks relative paths
- Verify frontmatter on migrated files matches the format your chosen conventions expect
- Confirm your AI tool can find and invoke the skills you want

---

## Schema migration discipline

Over time, your workspace conventions will evolve — new required fields, renamed directories, restructured templates. When that happens:

**Audit first.** Before changing anything, list everything that will be affected. How many files use the old convention? Which cross-references will break? A search takes minutes; fixing a botched migration takes hours.

**Plan the change.** Write down: what's changing, which files are affected, what the new state looks like, and how you'll verify success. For changes touching 10+ files, this plan is the difference between a clean migration and a debugging session.

**Execute with verification.** After making the changes, run a cross-reference check (the [pipeline](project-pipeline.md) G2 gate does this). Verify that frontmatter is valid on changed files. Check that your AI tool still resolves skills and references correctly.

**Review before committing.** For workspace-scale changes, run at least two independent checks before considering the migration done. The author of a change is the worst reviewer of that change — they see what they intended, not what they produced. An adversarial review (or even just a fresh `oa-review` run) catches what the author misses.

---

## Common patterns

**"I have a huge CLAUDE.md / instruction file."** Split it. Rules that apply to every session go in the BLUEPRINT. Domain-specific rules go in personas. Skill-specific rules go in skills. The goal is matching each rule to the [reliability tier](../reference/reliability-tiers.md) where it's most effective, not piling everything into one file.

**"I have governance but no skills."** Start with the four production skills Atlas ships (capture, think-it-through, extract-knowledge, review-context). They demonstrate the skill pattern. Build your own when you see a repeating workflow.

**"I have skills but no governance."** Add `workspace/governance/skill-conventions.md` and apply frontmatter to your existing skills. This gives your skills the metadata they need to be discoverable and reviewable. The governance directory doesn't have to be complex — even a single conventions file is a meaningful upgrade.

**"I want to keep my governance and just use Atlas templates."** Layer. Copy `workspace/templates/` from Atlas. Keep your governance directory as-is. Reference Atlas templates from your governance rules where useful.

---

*Open Atlas v1.2. See [governance-layering.md](../reference/governance-layering.md) for the conflict resolution model, and [project-pipeline.md](project-pipeline.md) for the build/ship process to follow for the migration itself.*

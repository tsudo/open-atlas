# Manifest — File Ownership Model

The `.atlas-manifest.yml` file declares which files Open Atlas owns vs which files you own. This matters when you want to update Open Atlas in the future and not lose your customizations.

You can ignore this entire doc until your first upgrade. The model exists to make upgrades safe; it doesn't affect day-to-day use.

---

## The three tiers

| Tier | Frontmatter | Who owns it | Upgrade behavior |
|---|---|---|---|
| **`core`** | `atlas_tier: core` | Open Atlas | Replaced on upgrade. Editing not recommended. |
| **`framework`** | `atlas_tier: framework` | Open Atlas | Replaced on upgrade *unless* you've customized it. Editing is expected for some files (templates, conventions you adopt). |
| **`user`** | `atlas_tier: user` | You | Never touched by upgrades. |

Every file in the Open Atlas distribution has one of these three tags in its frontmatter. Files you create should be tagged `user`.

---

## What each tier means in practice

### `core`

Files Open Atlas needs to function. Examples:

- Top-level governance files
- Schema definitions other things depend on
- The manifest itself

If you find yourself wanting to edit a `core` file, that's a signal to fork rather than customize. Open Atlas can't guarantee compatibility if `core` files diverge from the upstream version.

### `framework`

Files Open Atlas ships as starting points. The default templates, the default skill examples, the default hooks, the default personas. You're expected to *use* these — and to customize some of them as your workspace evolves.

The upgrade behavior for `framework` is the most nuanced of the three:

- **If you haven't touched the file**, an upgrade replaces it with the new framework version. You get the latest improvements.
- **If you have touched the file**, an upgrade preserves your version and surfaces the new framework version separately so you can choose to merge.

The detection is content-based. If the file's hash matches the shipped version, it's "untouched."

### `user`

Files you create from scratch, or framework files you've modified enough that they're meaningfully yours. Examples:

- Personas you've written
- Skills you've written
- Knowledge objects you've authored
- Drafts in `workspace/drafts/`
- Customized hooks

User files are never touched by upgrades. Open Atlas treats them as off-limits.

---

## The framework_overrides_user pattern

There's one exception to "user always wins": some framework files have user-tagged paths *inside* them, and the framework version of those paths is intentionally authoritative.

The most common case: `workspace/personas/per_open-atlas.md`. The persona file is shipped as `framework`, but it's *your* persona that you adopt and use. If you modify it heavily, the upgrade behavior should preserve your changes — but the *path* `workspace/personas/per_open-atlas.md` matters because tools and docs reference it by name.

The manifest handles this with a `framework_overrides_user_pattern` exception list. The shipped version names which paths are "framework owns the slot, user owns the content." For most workspaces you can ignore this — the default exceptions are correct.

---

## What the manifest looks like

The shape:

```yaml
# .atlas-manifest.yml
version: 1.1
tiers:
  core:
    - .atlas-manifest.yml
    - workspace/templates/schema/**/*.md
    - workspace/governance/skill-conventions.md
  framework:
    - workspace/templates/persona/**/*.md
    - workspace/templates/skill/**/*.md
    - workspace/personas/per_open-atlas.md
    - hooks/**/*.sh
    - hooks/**/*.md
    - docs/**/*.md
    - training/**/*.md
    - examples/**/*.md
  user:
    - workspace/skills/**/*.md
    - workspace/knowledge/**/*.md
    - workspace/drafts/**/*.md
    - workspace/personas/per_*.md
    - .claude/**
framework_overrides_user_pattern:
  - workspace/personas/per_open-atlas.md
```

The patterns are globs. Multiple patterns can match a single file; the manifest's resolution order is `framework_overrides_user_pattern` → `user` → `framework` → `core`.

Read the actual `.atlas-manifest.yml` at the repo root for the authoritative version.

---

## The frontmatter source of truth

The manifest is the *machine-readable* declaration. The frontmatter tag inside each file is the *human-readable* declaration. They should match, and if they don't, the manifest wins.

Why both? Because the frontmatter is what you see when you open a file in your editor. If you're about to edit `workspace/templates/skill/skill-template.md` and the frontmatter says `atlas_tier: framework`, you know upfront that the file is a Open Atlas-shipped template and editing it has upgrade implications. You don't have to consult the manifest to find out.

---

## How upgrades work (the model — not yet automated)

> **Honesty note:** Open Atlas v1.1 ships the manifest model and the tier tags. It does *not* yet ship an upgrade tool. The model is the design contract for how upgrades will work when the tool ships in a future release. For now, you upgrade by reading the changelog and manually merging.

The model:

1. You run the upgrade tool (or do the merge manually).
2. The tool reads the new shipment's manifest.
3. For each file in the new shipment:
   - If it's `core` → replace.
   - If it's `framework` and your version is unchanged → replace.
   - If it's `framework` and your version is modified → leave yours alone, surface the new version as a side-by-side diff.
   - If it's `user` → never touched.
4. New files in the shipment are added (unless they collide with a user file at the same path, in which case the user file wins).

This is the contract every Open Atlas release is committing to. Until the tool ships, you can do the same merge manually using the manifest as your guide.

---

## When to retag a file

If you modify a `framework` file enough that you consider it yours, retag it to `user` in the frontmatter. This tells future-you (and the upgrade tool) that the file is no longer eligible for replacement.

Conversely, if you write a new file that other parts of the workspace will depend on, consider tagging it `framework` rather than `user` — it'll get the same upgrade-protection treatment as the shipped framework files, which is what you want for shared infrastructure.

`core` is reserved for Open Atlas itself. Don't tag your own files `core`.

---

## Where to go next

- **The actual manifest** → `.atlas-manifest.yml` at the repo root
- **Compatibility table for AI tools** → [compatibility.md](compatibility.md)
- **Why Open Atlas separates conventions from enforcement** → [why-this-works.md](why-this-works.md)

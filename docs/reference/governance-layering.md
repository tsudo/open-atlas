# Governance Layering

How to layer your own governance conventions over Open Atlas without replacing the framework.

---

## The problem

You already have rules. Maybe your team uses a naming convention, your organization has a compliance requirement, or you've built your own governance structure over months of work. Adopting Open Atlas doesn't mean throwing that away.

The question is: when your rules conflict with Atlas conventions, which wins?

---

## The layering model

Open Atlas uses a three-tier ownership model (defined in `.atlas-manifest.yml`):

| Tier | Who owns it | What happens on upgrade |
|---|---|---|
| **core** | Atlas (immutable) | Never changes. LICENSE, manifest, SECURITY. |
| **framework** | Atlas (evolving) | Updated on upgrade with a diff prompt — you review before accepting. |
| **user** | You (absolute) | Atlas never touches it. Your files, your rules. |

Layering works by keeping Atlas framework files as *guidance* and placing your own governance files alongside them as *authority*. When the two conflict, yours wins.

---

## How to layer

### Naming convention

Use a prefix to distinguish your governance files from Atlas files:

- `atlas-*.md` — Atlas framework conventions (guidance)
- `[your-org]-*.md` — Your governance rules (authority)

Example in `workspace/governance/`:
```
workspace/governance/
├── skill-conventions.md          ← Atlas framework (guidance)
├── taxonomy.md                   ← Atlas framework (guidance)
├── pipeline.md                   ← Atlas framework (guidance)
├── acme-coding-standards.md      ← Your rules (authority)
├── acme-review-requirements.md   ← Your rules (authority)
```

When the AI reads the governance directory, both sets of rules are visible. The naming makes the hierarchy clear: your files override Atlas defaults in their domain.

### File permissions

Atlas framework files are read-only by convention. They change only on upgrade (and you review the diff first). Your governance files are read-write — you edit them anytime.

This separation means:
- Atlas upgrades don't overwrite your rules
- Your rules don't block Atlas upgrades
- Both coexist in the same directory

### Manifest exceptions

If you need to override a specific Atlas framework file (not just add alongside it), use the manifest's exception mechanism:

```yaml
exceptions:
  user_overrides_framework_pattern:
    - workspace/governance/skill-conventions.md  # Your fork of the Atlas version
```

This tells upgrade tooling: "this file looks like framework content, but we own it. Don't replace it on upgrade."

---

## Conflict resolution rules

When your governance and Atlas conventions disagree:

1. **Your explicit rules win over Atlas defaults.** If your `acme-coding-standards.md` says "all skills must have a security section" and Atlas's skill template doesn't include one, your rule applies.

2. **Atlas structural conventions apply unless you override them.** File naming, frontmatter fields, directory structure — these are Atlas defaults. They apply until you say otherwise.

3. **Stricter rule wins when both apply.** If Atlas says "skills should have a purpose section" and your rules say "skills must have a purpose section and a threat model," the stricter version applies.

4. **Overrides are explicit, not implicit.** If you want to change an Atlas default, write the override down. Don't rely on the AI inferring that you prefer something different.

---

## When to fork vs layer

**Layer** (recommended) when your rules add to Atlas conventions without contradicting them. Most governance needs fit this model — you're adding domain-specific requirements on top of the framework's structural defaults.

**Fork** when your rules fundamentally change how a framework file works. For example, if you need a completely different skill template with different required sections, fork `workspace/templates/skill/skill-template.md` into your own version and register it as a manifest exception. You'll miss Atlas template upgrades, but you'll have full control.

Most users should layer. Fork only when the Atlas default genuinely doesn't fit.

---

*Open Atlas v1.2. See the [manifest reference](manifest.md) for the three-tier ownership model, and [skill-conventions.md](../../workspace/governance/skill-conventions.md) for the default governance conventions.*

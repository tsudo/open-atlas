---
atlas_tier: framework
---

# Frontmatter Schema

Standard YAML metadata for durable markdown documents in your workspace.

**Why frontmatter matters:** It's the machine-readable context that helps your AI filter, prioritize, and route documents. A note tagged `draft` gets treated differently than one tagged `canonical`. Without frontmatter, every document looks the same to an agent.

---

## Required Fields

Every durable document should include these fields in its YAML frontmatter:

```yaml
---
title: [Human-readable title]
doc_type: [analysis | decision | procedure | kno | note | schema]
status: [draft | reviewed | canonical | archived]
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: [your-name]
tags: []
---
```

### Field Definitions

| Field | Purpose | Example |
| --- | --- | --- |
| `title` | Human-readable name | "API Authentication Decision" |
| `doc_type` | What kind of document this is | `decision`, `kno`, `procedure` |
| `status` | Where it is in its lifecycle | `draft` → `reviewed` → `canonical` |
| `created` | When first written | `2024-03-15` |
| `updated` | Last meaningful change | `2024-03-20` |
| `owner` | Who is responsible | Your name or team |
| `tags` | Categorization labels | `[security, api, architecture]` |

---

## Optional Fields

Add these when they provide useful signal:

```yaml
source: human | ai-assisted | imported | external
confidence: high | medium | low | evolving
sensitivity: public | internal | restricted
reviewed: true | false
```

---

## Document ID Convention (Optional)

For larger workspaces, a structured document ID helps with cross-referencing:

```
<TYPE>-<AREA>-<YYYYMMDD>-<NNN>
```

**Type codes:**

- `ANL` — analysis
- `DEC` — decision
- `KNO` — knowledge object
- `PRC` — procedure
- `NOTE` — general note

**Area codes** — define your own based on your domains (e.g., `ENG`, `OPS`, `SEC`).

**Example:** `DEC-ENG-20240315-001` — first engineering decision recorded on March 15, 2024.

---

## Status Lifecycle

```
draft → reviewed → canonical → archived
```

| Status | Meaning | Who changes it |
| --- | --- | --- |
| `draft` | Created, not yet checked | AI or human |
| `reviewed` | Verified for correctness | Human only |
| `canonical` | Preferred reference — stable truth | Human only |
| `archived` | No longer active, retained | Human or AI |

**Rule:** AI agents may create `draft` documents. Only humans promote to `reviewed` or `canonical`.

---

## Tips

- **Always include frontmatter** in documents that will be referenced later. Skip it for scratch notes.
- **Update the `updated` date** when you make meaningful changes, not just typo fixes.
- **Use tags consistently** — pick a controlled vocabulary and stick to it. `security` not sometimes `sec` and sometimes `infosec`.
- **Frontmatter on GitHub:** GitHub automatically strips YAML frontmatter from rendered markdown. No special handling needed.

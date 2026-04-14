# Skill Portability

How to design skills that work across multiple AI tools — and what to do when you switch tools.

---

## The two layers of a skill

Every skill has two layers, whether you think about it that way or not.

**The semantic center** is what the skill means: its purpose, function classification, inputs, outputs, constraints, approval boundaries, and quality gates. These don't change when you change AI tools. A "session retrospective" skill has the same purpose in Claude Code, Codex, or Cursor.

**The runtime expression** is how the skill is packaged for a specific tool: frontmatter format, directory layout, invocation syntax, permission declarations, and host-specific metadata. These change with every tool.

When you switch tools, you rewrite the runtime expression. The semantic center carries over.

---

## Semantic center fields

These are the fields that define what a skill is, independent of any tool:

| Field | What it captures |
|---|---|
| **Purpose** | What the skill does (2-3 sentences) |
| **Function** | `gate`, `steward`, `workflow`, or `utility` — see [skill-functions.md](skill-functions.md) |
| **Freedom level** | `high`, `medium`, or `low` — how much latitude the AI has |
| **Inputs** | What the skill needs to run |
| **Outputs** | What the skill produces |
| **Procedure** | The steps the skill follows |
| **Approval boundaries** | Where the skill must pause for confirmation |
| **Constraints** | What the skill explicitly does not do |
| **Quality gates** | Conditions for valid completion |

If you can fill in these fields, you have a portable skill definition. The tool-specific packaging is a translation step, not a design step.

---

## Runtime expression fields

These vary by tool. Examples:

| Concern | Claude Code | Codex | Cursor | Generic |
|---|---|---|---|---|
| **File location** | `.claude/skills/` or project skills dir | `.codex/skills/` | Referenced in `.cursorrules` | Any readable path |
| **Frontmatter format** | YAML with `name`, `description`, metadata | `name` + `description` only | No frontmatter (inline) | Varies |
| **Invocation** | Slash command or hotword | Direct mention or path reference | Rule-triggered | Paste into prompt |
| **Permissions** | `allowed-tools` field | Config-level tool allowlists | N/A | N/A |
| **Context loading** | Automatic on invocation | Manual or path-based | Rule-scoped | Manual |

The runtime layer changes. The semantic center doesn't. When you move a skill between tools, translate the packaging — don't redesign the skill.

---

## Worked example

Here's the same skill described at the semantic level and projected into two tools.

### Semantic center

```
Purpose: Run a structured retrospective after a work session.
Function: workflow
Freedom level: medium
Inputs: Current session context, optional mode (quick/full)
Outputs: Findings categorized by type, routing plan, approved writes
Approval boundaries: Before any file writes, mode confirmation in adaptive mode
Constraints: Does not modify governance files, does not auto-write
Quality gates: All approved items routed, completion status declared
```

### Claude Code projection

A YAML-frontmatter markdown file at `workspace/skills/retro.md` with `function: workflow`, `freedom_level: medium`, `hotwords: [retro, retrospective]`. Full procedure section. Invoked via `/retro` or hotword.

### Codex projection

A markdown file at `.codex/skills/retro/SKILL.md` with `name` and `description` frontmatter. Body instructions written for Codex's execution model (more prescriptive, explicit stop-and-wait language at approval boundaries since Codex can feel more autonomous). Invoked via direct path reference.

### What changed between projections

The purpose, function, freedom level, inputs, outputs, approval boundaries, constraints, and quality gates are identical. The frontmatter shape, directory location, invocation method, and body writing style changed. That's the translation — packaging, not design.

---

## When portability matters

**You use multiple tools.** If you run Claude Code for some work and Codex for other work, portable skill definitions prevent you from maintaining two separate skill sets that drift apart.

**You might switch tools.** If your primary tool changes (new model, new IDE, organizational mandate), portable skills survive the transition. You rewrite the packaging once; the procedures carry over.

**You share skills with others.** If a colleague uses a different tool, a semantic-center definition is the format that's useful to both of you. They translate it into their tool; you translate it into yours.

**You don't (yet).** If you use one tool and don't plan to change, portability is nice-to-have, not must-have. Write your skills in your tool's native format and don't add abstraction you don't need. You can always extract the semantic center later if the need arises.

---

## The portability test

For any skill you've built, ask: if I had to recreate this skill in a different AI tool tomorrow, what would I carry over and what would I rewrite?

If the answer is "I'd rewrite everything," the skill is tightly coupled to its tool. That's not wrong — it may be the right choice for a tool-specific feature. But if the skill's value is in its procedure (and most skills' value is), the procedure should survive a tool change.

---

*Open Atlas v1.2. See [skill-functions.md](skill-functions.md) for the function classification system, and the [skill template](../../workspace/templates/skill/skill-template.md) for the full field set.*

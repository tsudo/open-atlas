# Core Concepts

Open Atlas has five concepts. You can run a useful workspace with two of them. The other three are when you want more.

---

## The five concepts

| Concept | What it is | Where it lives |
|---|---|---|
| **Workspace** | The directory the AI works in. Has a predictable structure so the AI can find what it needs. | `workspace/` |
| **Persona** | A named role the AI can adopt. Bounded voice, opinions, and behaviors. | `workspace/personas/` |
| **Skill** | A defined workflow the AI can execute. Inputs, procedure, outputs, completion status. | `workspace/skills/` |
| **Hook** | A script that runs before or after a tool call to enforce conventions deterministically. v1.1 ships hook *designs* (explainers); working scripts ship in v1.2. | `hooks/` |
| **Manifest** | A file ownership model that says which files Open Atlas owns vs which files you own. | `.atlas-manifest.yml` |

---

## Minimum viable adoption — just two

You can get real value out of Open Atlas with **just the workspace and one persona**. That's it.

The path:

1. **Clone the repo.** You now have a workspace.
2. **Read `workspace/README.md` with your AI** at the start of each session. The AI now knows where things go.
3. **Use `per_open-atlas` as your default persona** when you want grounded, structured conversation. Or don't — the workspace structure works without a persona, too.

Everything else is additive:

- **Skills** when you find yourself doing the same multi-step workflow more than twice
- **Hooks** when you've felt the pain of a convention being ignored and want the AI to be physically prevented from skipping it
- **More personas** when you need the AI to switch between meaningfully different stances
- **Manifest** when you start customizing files and need to remember which ones came from Open Atlas vs which ones you wrote

You do not need to learn all five concepts on day one. You probably should not.

> **Want even less?** The [`quick-start/`](../../quick-start/) directory ships an *even more minimal* path — two files (`AI-INSTRUCTIONS.md` and `BLUEPRINT.md`) you copy into any project root, no workspace structure required. The two paths are complementary: the quick-start path is the absolute floor (use it when you don't want a `workspace/` directory at all); the minimum-viable-adoption path on this page is the floor of the full workspace experience (use it when you want the structure but only need to learn two concepts to start).

---

## The five concepts in depth

### Workspace

The `workspace/` directory is the AI's working environment. It has a predictable structure — `personas/`, `skills/`, `templates/`, `knowledge/`, `drafts/`, `governance/` — so the AI can find what it needs without you having to explain the layout every session.

The structure is opinionated but not rigid. Rename directories, add new ones, move things around. Whatever ends up in `workspace/README.md` is what the AI reads to understand your version of the layout.

### Persona

A persona is a named role with bounded voice, opinions, and behaviors. Personas are markdown files with frontmatter and a defined structure (purpose, voice, behaviors, what it does NOT do). When you activate a persona, the AI reads the file and adopts the role.

Personas are not characters. They're not a way to make the AI "fun." They're a way to get *consistent* outputs from the AI when you need a particular kind of conversation — advisory, adversarial, classification, etc.

The shipped example personas (`per_advisor`, `per_reviewer`, `per_librarian`) demonstrate three distinct shapes. See [creating-personas.md](../day-2/creating-personas.md) when you want to build your own.

### Skill

A skill is a defined workflow the AI can execute. Skills have:

- **Hotwords** — short trigger phrases you can use to invoke the skill
- **Function class** — `gate`, `steward`, `workflow`, or `utility` (see [skill-functions.md](../reference/skill-functions.md))
- **Procedure** — numbered steps the AI follows
- **Output contract** — what the skill produces
- **Completion status** — `DONE | DONE_WITH_NOTES | BLOCKED | NEEDS_CONTEXT`

Skills are how you make repeated workflows reliable. Instead of explaining the process every time, you write it down once and trigger it with a hotword.

### Hook

A hook is a script that runs before or after a tool call. It gets the tool's input on stdin and decides: allow, flag, or block. Hooks make conventions enforceable instead of just hopeful.

**v1.1 ships hook designs, not working scripts.** The `hooks/` directory contains explainers that describe what each hook should catch, why, and how to wire it. Working scripts and a test harness ship in v1.2. If you want to wire your own version of any of these hooks now, the explainers tell you what to enforce and why.

Hooks are the most "infrastructure-y" part of Open Atlas. You can run a perfectly useful workspace without them. Skip the hooks directory until you've felt the gap. See [../day-2/using-hooks.md](../day-2/using-hooks.md) for the "is this for you yet?" preflight.

### Manifest

The `.atlas-manifest.yml` file declares which files Open Atlas owns vs which files you own. Three tiers:

- **`core`** — files Open Atlas needs to function. Upgrades replace these.
- **`framework`** — files Open Atlas ships as starting points. Upgrades may replace these unless you've customized them.
- **`user`** — files you've created or customized. Upgrades never touch these.

This matters when you want to update Open Atlas in the future and not lose your work. See [../reference/manifest.md](../reference/manifest.md) for the full model. You can ignore the manifest entirely until your first upgrade.

---

## What Open Atlas is, in one paragraph

Open Atlas is a governed workspace for AI-assisted work. It gives the AI a predictable place to read context, write drafts, and follow conventions — so you spend your time on the actual work, not on re-explaining where things go. You can adopt as much or as little of it as you need. Two concepts is enough to start.

---

## What Open Atlas is *not*

- **Not a framework you have to learn before you can use it.** Two concepts and a `read this file` instruction get you started.
- **Not a sandbox or security boundary.** Hooks are tripwires, not walls.
- **Not a replacement for your AI tool.** Open Atlas runs *inside* Claude Code, Cursor, Codex, or whatever you're using.
- **Not a methodology with a capital M.** It's a set of conventions you can edit, replace, or ignore.
- **Not an organizational governance program.** It governs how your AI behaves inside your workspace. If you need to govern how your *organization* adopts AI more broadly — acceptable use, risk tiers, board reporting — that's a different problem at a different layer.

---

## Where to go next

- **Run your first session** → [first-session.md](first-session.md)
- **Learn to build your own personas** → [../day-2/creating-personas.md](../day-2/creating-personas.md)
- **Learn to build your own skills** → [../day-2/creating-skills.md](../day-2/creating-skills.md)
- **Understand the design** → [../reference/why-this-works.md](../reference/why-this-works.md)

<p align="center">
  <img src="assets/open-atlas-logo.jpg" alt="Open Atlas" width="200">
</p>

<h1 align="center">Open Atlas</h1>

<p align="center"><strong>Give your AI a workspace it can navigate — not just a pile of files.</strong></p>

<p align="center">
  <img src="https://img.shields.io/badge/License-CC--BY--4.0-lightgrey.svg" alt="License: CC-BY-4.0">
  <img src="https://img.shields.io/badge/Version-v1.2-blue.svg" alt="Version: v1.2">
  <img src="https://img.shields.io/badge/AI-Workspace%20Framework-purple.svg" alt="AI Workspace">
</p>

Your folder structure is part of every AI prompt, whether you designed it that way or not. **Open Atlas gives your AI a workspace it can navigate, named personas it can adopt, and skills it can execute** — so the AI works *with* your conventions instead of around them.

**See the difference:** [examples/before-after.md](examples/before-after.md) — same prompt, two workspaces. Five minutes.

> **You do not need all of this.** The minimum-viable adoption path is two concepts (workspace + one persona) and zero hooks. Start there. Add more when you've felt the gap. See [docs/day-1/core-concepts.md](docs/day-1/core-concepts.md).

---

## Contents

- [Walkthrough — see it in action](#walkthrough--see-it-in-action)
- [Quick Start](#quick-start)
- [Three paths in](#three-paths-in)
- [What Open Atlas does](#what-open-atlas-does)
- [What Open Atlas does NOT do](#what-open-atlas-does-not-do)
- [What's inside](#whats-inside)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [License](#license)

---

## Walkthrough — see it in action

Before reading anything else, look at what Open Atlas does in practice:

**[examples/walkthrough.md](examples/walkthrough.md)** — A messy "should I use SQLite or Postgres?" question taken through three templates: analysis → decision → durable knowledge. Three artifacts. Each builds on the last. You can read it in five minutes.

**[examples/before-after.md](examples/before-after.md)** — Side-by-side: the same prompt asked of an AI working in a junk-drawer directory vs. an AI working in an Open Atlas workspace. The difference is not subtle.

If those land for you, the rest of this README will make sense. If they don't, this framework probably isn't for you yet.

---

## Quick Start

Three steps. No installation, no dependencies, no runtime. Each step is one move.

**Step 1.** Clone the repo (or download the zip).

```bash
git clone https://github.com/tsudo/open-atlas.git
cd open-atlas
```

**Step 2.** Open the directory in your AI tool — Claude Code, Cursor, Codex, or anything that can read files.

**Step 3.** Paste this one sentence to your AI:

> Read `workspace/README.md` and walk me through what to do next.

That's it. The AI reads the workspace orientation, recognizes the structure, and routes you to the right next move based on what you tell it you want to do.

When you want more — slash commands, custom skills, your own personas — see [docs/day-2/](docs/day-2/). When you want to understand the design — see [docs/reference/why-this-works.md](docs/reference/why-this-works.md).

---

## Three paths in

Different starting points need different first moves.

| You are... | Start here |
|---|---|
| **Just installed Claude Code (or Cursor / Codex)** | [docs/day-1/first-session.md](docs/day-1/first-session.md) → [docs/day-1/core-concepts.md](docs/day-1/core-concepts.md). One session, two concepts, no wiring required. |
| **Building an AI workflow for your team** | [docs/day-1/](docs/day-1/) for orientation → [docs/day-2/](docs/day-2/) for creating personas, skills, hooks → [docs/reference/](docs/reference/) for the design rationale. |
| **Forking it for your own workflow** | [docs/reference/why-this-works.md](docs/reference/why-this-works.md) first — so you know what the conventions are doing before you change them. Then [docs/reference/manifest.md](docs/reference/manifest.md) for the file ownership model. |

---

## What Open Atlas does

- **Gives the AI a predictable workspace** — `workspace/` with conventions for personas, skills, knowledge, drafts, templates
- **Ships named personas** the AI can adopt — `per_open-atlas` (default), plus three example personas in `training/kits/persona-starter/examples/` (`per_advisor`, `per_reviewer`, `per_librarian`)
- **Ships eight production skills + a starter** in `workspace/skills/` — `capture` (capture a thought), `think-it-through` (decision help with anti-sycophancy gates), `extract-knowledge` (turn an insight into a durable artifact), `review-context` (quick workspace audit), `retro` (session retrospective), `oa-skill-creator` (guided skill creation), `oa-review` (full structural audit), `oa-update` (upstream update checker), and `my-next-skill` (starter scaffold). Each skill composes a reasoning strategy and the base output contract.
- **Ships hook designs as explainers** — credential-leak detection, prompt-injection flagging, convention checks. Working scripts and a test harness ship in v1.2; the v1.1 explainers are the design contract you can implement in your own scripting language now if you want.
- **Includes hardened templates** for personas and skills with required sections (especially "what this does NOT do") so what you build is consistent
- **Ships every example with the kit that built it** — the `training/` directory has the same starter kits, prompts, and walkthroughs you'd use to build your own personas and skills

---

## What Open Atlas does NOT do

Be explicit about the boundaries:

- **Not a security boundary.** Hooks are tripwires, not jails.
- **Not a sandbox.** The AI can do whatever your tool allows it to do.
- **Not a methodology.** It's a set of conventions you can edit, replace, or ignore.
- **Not an agent framework.** No LangChain, no AutoGen, no orchestration runtime.
- **Not vendor-locked.** Works with any AI tool that can read markdown files.
- **Not an organizational governance program.** It governs AI behavior *inside* a workspace. If you need acceptable-use policy, risk tiers, or board reporting — that's a different layer. See the SC handoff in [docs/reference/why-this-works.md](docs/reference/why-this-works.md).
- **Not a learning management system.** The feedback loop is light by design.

---

## What's inside

```text
open-atlas/
├── README.md                       ← You are here
├── LICENSE                         ← CC-BY-4.0
├── SECURITY.md                     ← Reporting and scope
├── DISCLAIMER.md                   ← Not professional advice
├── ONBOARDING.prompt.md            ← Single-pass setup prompt for AI tools
├── .atlas-manifest.yml             ← File ownership manifest (core / framework / user)
│
├── workspace/                      ← The starter workspace — copy or clone in place
│   ├── README.md                   ← Read this first in every session
│   ├── BLUEPRINT.md                ← The AI's map of the workspace
│   ├── governance/                 ← Conventions (skill conventions, taxonomy)
│   ├── templates/                  ← Persona, skill, doc, schema templates
│   ├── personas/                   ← per_open-atlas (your default persona)
│   ├── skills/                     ← 8 production skills + my-next-skill starter
│   ├── knowledge/                  ← Durable reference material
│   ├── drafts/                     ← Work in progress
│   └── archive/                    ← Completed work
│
├── hooks/                          ← Hook design notes (working scripts in v1.2)
│   ├── README.md
│   ├── check-credential-leak.md
│   ├── check-injection-egress.md
│   └── check-pre-action.md
│
├── docs/                           ← Three-layer documentation
│   ├── README.md
│   ├── day-1/                      ← First-session walkthrough + core concepts
│   ├── day-2/                      ← Creating personas, skills, hooks; maintenance
│   └── reference/                  ← Anti-patterns, output contracts, manifest, compatibility, why-this-works
│
├── training/                       ← Pedagogical material (kits, prompts, examples)
│   ├── README.md
│   ├── kits/persona-starter/       ← Structured AI-guided interview to build a persona
│   ├── kits/skill-starter/         ← Same for skills
│   └── prompts/                    ← Single-pass alternatives to the kits
│
├── examples/                       ← End-to-end and side-by-side examples
│   ├── walkthrough.md              ← Messy problem → analysis → decision → KNO
│   ├── before-after.md             ← Junk-drawer vs Open Atlas, same prompt
│   ├── sample-kno.md
│   ├── sample-decision.md
│   └── sample-human-context.md
│
└── Releases/
    └── v1.1/README.md              ← Release notes
```

---

## Documentation

- **[docs/README.md](docs/README.md)** — full documentation index
- **[docs/day-1/](docs/day-1/)** — first session, core concepts
- **[docs/day-2/](docs/day-2/)** — creating personas, creating skills, using hooks, workspace maintenance
- **[docs/reference/](docs/reference/)** — anti-patterns, feedback loop, skill functions, output contracts, reliability tiers, manifest, compatibility, why-this-works

---

## Contributing

Open Atlas is maintained as an open framework. Contributions welcome:

- **Found a bug or gap** → open an issue
- **Have a template, hook, or example to share** → open a PR
- **Adapted this for your domain** → I'd love to hear about it

See [SECURITY.md](SECURITY.md) for security-related reporting.

---

## License

[CC-BY-4.0](LICENSE) — use it freely, adapt it to your needs. Attribution required.

See [DISCLAIMER.md](DISCLAIMER.md) for important notes on scope and liability.

---

<p align="center">
  <img src="assets/open-atlas-logo.jpg" alt="Open Atlas" width="80"><br>
  <strong>Open Atlas</strong> — a free AI workspace framework<br>
  Created by <a href="https://keithcrawford.me">Keith Crawford</a> ·
  <a href="https://open-atlas.dev">open-atlas.dev</a> ·
  <a href="https://x.com/tsudo">@tsudo</a> ·
  CC-BY-4.0
</p>

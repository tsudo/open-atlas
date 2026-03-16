<p align="center">
  <img src="assets/open-atlas-logo.jpg" alt="Open Atlas" width="200">
</p>

<h1 align="center">Open Atlas</h1>

<p align="center"><strong>Give your AI a workspace it can navigate — not just a pile of files.</strong></p>

<p align="center">
  <img src="https://img.shields.io/badge/License-CC--BY--4.0-lightgrey.svg" alt="License: CC-BY-4.0">
  <img src="https://img.shields.io/badge/Version-v1-blue.svg" alt="Version: v1">
  <img src="https://img.shields.io/badge/AI-Workspace%20Framework-purple.svg" alt="AI Workspace">
</p>

Your folder structure is part of every AI prompt — whether you designed it that way or not. The way you organize information determines what AI tools can find, retrieve, and reason about. Most people never think about this. Open Atlas gives you the foundation: a workspace structure, metadata standards, and document templates that make AI agents more effective from day one.

This is not a prompt library, a Claude Code config, or an automation framework. It's an opinionated, human-governed context architecture that works with any AI tool that can read files.

---

## Contents

- [Quick Start](#quick-start)
- [Two-File Minimum](#two-file-minimum)
- [What's Inside](#whats-inside)
- [Choose Your Path](#choose-your-path)
- [Who This Is For](#who-this-is-for)
- [Blog Series](#blog-series)
- [Contributing](#contributing)
- [License](#license)

---

## Quick Start

### Option A: Let your AI do it (if your tool can clone repos)

```text
Clone the Open Atlas repo from github.com/tsudo/open-atlas into a temp folder. Copy the workspace/ folder and the quick-start/AI-INSTRUCTIONS.md file into my current working directory. Then read the ONBOARDING.prompt.md and walk me through the setup.
```

Paste that into Claude Code, Cursor, or any agentic tool with filesystem and network access. The AI handles the rest.

> **Note:** Some tools (like Codex) run in a sandbox and can't clone repos. If Option A doesn't work, use Option B — it always works.

### Option B: Manual setup (works with any tool)

1. **Copy the `workspace/` folder** into your project root
2. **Open `ONBOARDING.prompt.md`**, copy the prompt, and paste it into an agentic AI tool (Claude Code, Cursor, Codex)
3. **Answer 4 questions** — the AI will set up your workspace and create your first human context file
4. **Start using templates** — try the analysis prompt on a real problem

Either way, you'll have a structured, AI-navigable workspace in about 30 minutes.

**Want to see what this looks like in practice?** The [walkthrough](examples/walkthrough.md) takes a messy problem through three templates and shows the payoff:

| Step | Template | Output |
| --- | --- | --- |
| Analyze the problem | `analysis.prompt.md` | Structured options + recommendation |
| Record the decision | `decision-record.md` | Decision with rationale and risks |
| Extract durable knowledge | `knowledge-object.md` | Reusable reference for future decisions |

Three steps. Three artifacts. Each one builds on the last. That's what "templates enable compounding" looks like.

---

## Two-File Minimum

Don't want the full workspace? Start with two core files and a few folders:

1. Copy [`quick-start/AI-INSTRUCTIONS.md`](quick-start/AI-INSTRUCTIONS.md) and [`quick-start/BLUEPRINT.md`](quick-start/BLUEPRINT.md) into your project root
2. Create starter folders: `mkdir -p knowledge/decisions projects drafts archive`
3. Wire it to your tool — rename the instructions file ([see wiring table](quick-start/))
4. Start working — your AI reads both files automatically

Five minutes of setup, immediately better AI output. Upgrade to the full workspace when you're ready.

---

## What's Inside

```text
open-atlas/
├── README.md                    ← You are here
├── LICENSE                      ← CC-BY-4.0
├── DISCLAIMER.md                ← Not professional advice
├── ONBOARDING.prompt.md         ← Paste into your AI to get started
│
├── quick-start/                 ← Two-file minimum (AI-INSTRUCTIONS.md + BLUEPRINT.md)
│
├── workspace/                   ← The starter kit — copy this into your project
│   ├── README.md                ← Start here
│   ├── BLUEPRINT.md             ← Your AI's map of the workspace
│   ├── governance/
│   │   └── taxonomy.md          ← 10 core AI workspace concepts defined
│   ├── templates/
│   │   ├── prompt/              ← Thinking patterns (analysis, decision, extraction)
│   │   ├── doc/                 ← Document skeletons (7 templates)
│   │   └── schema/              ← Metadata standards (frontmatter, knowledge objects)
│   ├── personas/
│   │   └── per_open-atlas.md    ← Workspace advisor persona (working example)
│   ├── context/
│   │   └── human-operating-model.md  ← Teach your AI how you think
│   ├── knowledge/               ← Durable reference material by topic
│   ├── projects/                ← Your active projects
│   ├── drafts/                  ← Work in progress
│   └── archive/                 ← Completed work
│
├── examples/                    ← Filled-in examples for reference
│   ├── walkthrough.md           ← End-to-end: messy problem → analysis → decision → knowledge
│   ├── sample-kno.md            ← Example knowledge object
│   ├── sample-decision.md       ← Example decision record
│   └── sample-human-context.md  ← Example human operating model (fictional)
│
└── docs/
    └── why-this-works.md        ← Design philosophy
```

### Templates Included

**Prompt templates** — structured thinking patterns:

| Template | Use when you need |
| --- | --- |
| `analysis.prompt.md` | Structured reasoning, tradeoff evaluation, planning |
| `decision.prompt.md` | Recommendation between choices, go/no-go decisions |
| `extraction.prompt.md` | Facts and structure from raw text or conversations |

**Document templates** — skeletons for recurring document types:

| Template | Use when you need |
| --- | --- |
| `readme.template.md` | A README for any folder, project, or tool |
| `decision-record.md` | To document a deliberated choice with rationale |
| `knowledge-object.md` | Durable reference knowledge (frameworks, patterns, how-tos) |
| `project-brief.md` | A scoped project definition with success criteria |
| `research-note.md` | An investigation with findings and confidence levels |
| `procedure.md` | Step-by-step operational instructions |
| `meeting-notes.md` | Meeting capture with decisions and actions |

**Schemas** — metadata standards:

| Schema | What it defines |
| --- | --- |
| `frontmatter.md` | YAML metadata fields for all documents |
| `knowledge-object.md` | Knowledge object types, lifecycle, and naming |

---

## Choose Your Path

| Starting point | What to do |
| --- | --- |
| **Just installed an AI tool** | [Quick Start](#quick-start) — copy workspace/, run onboarding prompt |
| **Want the minimum** | [Two-File Minimum](#two-file-minimum) — AI-INSTRUCTIONS.md + BLUEPRINT.md, five minutes |
| **Have existing files** | Copy `workspace/` alongside your files, tell the onboarding prompt "I have existing files" — it'll do a gap analysis |
| **Already organized (PARA, etc.)** | Browse `workspace/` and cherry-pick templates, schemas, or the blueprint pattern |

---

## Who This Is For

**Builders who use AI daily and know their workspace is a mess.** You've been meaning to organize your files. Open Atlas is the structure you'd build if you had a weekend — except it's already built.

**AI engineers who want governance without overhead.** You know metadata matters. You know templates compound. You just need a starting point that isn't over-engineered.

**Someone who just installed Claude Code or Cursor.** A friend sent you this link. Start with the [two-file quick start](quick-start/) or the full onboarding prompt. You'll be set up in 30 minutes.

---

## Blog Series

These posts explain the thinking behind Open Atlas:

1. **[Context Engineering: Why Your Folder Structure Is an AI Problem](https://keithcrawford.me/blog/context-engineering-folder-structure-ai)** — The insight that started it all
2. **Output Contracts, Schemas, and the Layer Everyone Skips** *(coming soon)*
3. **Teaching Your AI Who You Are: Human Context Files** *(coming soon)*

---

## Contributing

Open Atlas is maintained by one person and contributions are welcome. If you:

- **Found a bug or gap** — open an issue
- **Have a template to share** — open a PR
- **Adapted this for your domain** — I'd love to hear about it

---

## License

[CC-BY-4.0](LICENSE) — use it freely, adapt it to your needs. Attribution required.

See [DISCLAIMER.md](DISCLAIMER.md) for important notes on scope and liability.

---

<p align="center">
  <img src="assets/open-atlas-logo.jpg" alt="Open Atlas" width="80"><br>
  <strong>Open Atlas</strong> — an opinionated AI workspace framework<br>
  Created by <a href="https://keithcrawford.me">Keith Crawford</a> ·
  <a href="https://open-atlas.dev">open-atlas.dev</a> ·
  <a href="https://x.com/tsudo">@tsudo</a> ·
  CC-BY-4.0
</p>

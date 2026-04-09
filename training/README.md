# Training

This directory contains pedagogical material for *you*, the human. It's how you learn to build personas, skills, and workflows that work the way Open Atlas expects.

---

## Vocabulary discipline: training vs learning

Open Atlas distinguishes two words that are often used interchangeably:

- **Training** (this directory) — pedagogical material, authored once, updated when conventions change. Curriculum.
- **Learning** — the workspace getting better at its job through POSITIVE_SIGNAL events, feedback memories, and periodic audits. Continuous, dynamic. Lives in the operational workspace, not here.

Training is what you read when you're learning to use Open Atlas. Learning is what your workspace does as you use it. See [../docs/reference/feedback-loop.md](../docs/reference/feedback-loop.md) for the learning side.

---

## What's in this directory

```
training/
├── README.md                         ← You are here
├── kits/
│   ├── persona-starter/              ← Build your first persona
│   │   ├── SETUP.md                  ← AI-guided interview
│   │   ├── VERIFY.md                 ← Post-creation verification
│   │   ├── templates/                ← Kit-local copy of persona-template.md
│   │   └── examples/                 ← per_advisor, per_reviewer, per_librarian
│   └── skill-starter/                ← Build your first skill
│       ├── SETUP.md
│       ├── VERIFY.md
│       ├── templates/
│       └── examples/                 ← capture, publish-check, review-context
└── prompts/
    ├── create-persona.prompt.md      ← Single-pass alternative to the persona kit
    └── create-skill.prompt.md        ← Single-pass alternative to the skill kit
```

---

## When to use what

| You want to... | Use |
|---|---|
| **Build your first persona** | `kits/persona-starter/SETUP.md` — structured interview, takes 30–45 min |
| **Build subsequent personas faster** | `prompts/create-persona.prompt.md` — single-pass conversation |
| **Build your first skill** | `kits/skill-starter/SETUP.md` — structured interview with hotword conflict checking |
| **Build subsequent skills faster** | `prompts/create-skill.prompt.md` — single-pass conversation |
| **See examples** | `kits/*/examples/` — three personas, three skills covering the major shapes |
| **Verify what you built** | `kits/*/VERIFY.md` — post-creation check categories with PASS/FAIL gates |

---

## The kit vs. prompt distinction

Both produce a working persona or skill. The difference is the experience:

- **Kits** are structured. They walk you through every section, push back on vague answers, run hotword conflict checks against existing files, and verify the output against a checklist. Use kits the first time you build something, when you don't yet know what "good" looks like.
- **Prompts** are single-pass. They cover the same steps in less ceremony. Use prompts when you've built one or two of the thing already and you trust your own judgment about what to include.

Neither is "better." Pick based on whether you want guidance or speed.

---

## What you're learning to build

The kits and prompts both enforce the same shape — the shape that makes personas and skills consistent across an Open Atlas workspace:

- **Required frontmatter** (`atlas_tier: user`, function class, hotwords)
- **Required sections** (Purpose, What This Does, **What This Does NOT Do**, Inputs, Procedure, Output Contract, Completion Status, Authority Boundaries)
- **Function-specific discipline** (NO-HEDGING for gates, POSITIVE_SIGNAL for stewards/gates)
- **Voice quality** (no hedging vocabulary, opinionated framing, push-back behavior)

These aren't arbitrary rules. Each one came from a specific failure mode the kit was designed to prevent. See [../docs/reference/anti-patterns.md](../docs/reference/anti-patterns.md) for the failure modes themselves.

---

## What training is NOT

- **Not a prerequisite.** You can use Open Atlas without going through any of the kits. The minimum-viable adoption path in [../docs/day-1/core-concepts.md](../docs/day-1/core-concepts.md) doesn't require you to build anything new.
- **Not a certification.** No grades, no signoff. The kits exist to make your output good, not to certify it.
- **Not the operational workspace.** Files in `training/` are pedagogical. The actual personas and skills you use live in `workspace/`. Don't mix them.
- **Not where the workspace learns.** That's the feedback loop, in `workspace/` and the docs reference layer. Training is for *you*.

---

## Where to go next

- **Build your first persona** → [kits/persona-starter/SETUP.md](kits/persona-starter/SETUP.md)
- **Build your first skill** → [kits/skill-starter/SETUP.md](kits/skill-starter/SETUP.md)
- **Understand the design rationale** → [../docs/reference/why-this-works.md](../docs/reference/why-this-works.md)

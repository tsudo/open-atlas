# AI Workspace Taxonomy

Canonical definitions for the building blocks of an AI-augmented workspace. These terms prevent confusion when you and your AI discuss capabilities, structure, and behavior.

---

## Capability Stack

Terms organized by layer — highest-level to most atomic:

```
┌─────────────────────────────────────────────┐
│  Governance   rules, authority, permissions  │  ← defines what is allowed
├─────────────────────────────────────────────┤
│  Persona      identity, style, domain focus  │  ← shapes how the agent behaves
├─────────────────────────────────────────────┤
│  Skill        executable, reusable workflow  │  ← composes strategies + contracts + tools
├─────────────────────────────────────────────┤
│  Tool         atomic, discrete function call │  ← building block; not composed
└─────────────────────────────────────────────┘

Composable Primitives (loaded by skills, not directly executable):
  Strategy Template    — reusable reasoning pattern (how to think)
  Output Contract      — reusable output envelope (how to format)
  Function Class       — categorical kind of skill (gate / steward / workflow / utility)

Reference Layer (not executable; informs the above):
  Protocol   — binding rules for a class of interaction
  Procedure  — documented steps for a repeatable outcome
  Template   — reusable document or prompt structure
```

---

## Term Definitions

### Agent

An AI system with agency — the capacity to perceive context, reason, plan, and take actions toward a goal, potentially invoking tools, spawning sub-agents, or executing multi-step workflows.

- An agent is not a persona. A persona shapes how an agent behaves; it does not create a second agent.
- Automation is not autonomy. Agent actions are bounded by governance and human instruction.

---

### Tool

A discrete, atomic function an agent can call — a single operation with defined inputs and outputs. Tools are the lowest-level building blocks; they do not themselves invoke other tools or compose logic.

- Tools are not skills. A tool is atomic; a skill composes tools into a workflow.
- Tools are provided by the runtime (e.g., file read, web search, code execution) or by external integrations.

---

### Skill

A packaged, executable, reusable workflow invoked by an agent — typically triggered by a keyword or explicit call. Skills compose strategy templates, output contracts, and tools to accomplish a bounded objective.

- Skills are agent-executable. They run; they do not just document.
- A skill is not a tool (atomic) and not a procedure (reference only).
- Skills have a defined trigger, an input contract, and an expected output.
- Skills belong to one of four function classes (see Function Class below).
- Skills compose composable primitives — strategy templates for reasoning, output contracts for formatting — instead of reinventing them inline. This is the load-bearing architectural pattern in v1.1.

---

### Persona

A persistent behavioral profile applied to an agent — defines domain focus, communication style, decision heuristics, and authority scope for a session.

- A persona shapes identity, not capability. It does not grant new tools or change file access.
- One persona is active at a time. Personas are activated by explicit request.
- Personas are guidance, not authority. Governance overrides any persona.

---

### Procedure

A documented, repeatable sequence of steps for a defined outcome. Procedures are human-readable reference documents — they describe what to do, not how to trigger it automatically.

- Procedures are not executable. They are referenced by skills and agents, but do not run themselves.
- A procedure is not a skill. The skill runs; the procedure documents the logic.

---

### Protocol

A formal, binding specification for how to handle a class of interaction — defines rules and required steps that must be followed, not merely consulted.

- Protocols are binding within their defined scope. Procedures are advisory.
- A protocol governs a class of events (e.g., all session closes, all escalations). A procedure governs a specific repeatable task.

---

### Template

A reusable document or prompt structure with defined sections and placeholders — not executable, not a procedure. Used for prompts, project scaffolding, output formats, and document skeletons.

- Templates are filled in at task time. They define structure, not logic.
- Four subtypes:
  - **Prompt templates** — thinking patterns with input placeholders, user-pasted into conversation (analysis, decision, extraction)
  - **Document templates** — document skeletons with required sections (decision records, briefs, knowledge objects)
  - **Strategy templates** — reusable *reasoning* patterns that skills compose internally (see Strategy Template below)
  - **Output contract templates** — reusable *output envelope* patterns that skills compose internally (see Output Contract below)

---

### Function Class

The categorical kind of a skill, declared in skill frontmatter as `function: gate | steward | workflow | utility`. Each class enforces its own discipline.

- **`workflow`** — multi-step process from input to output. The most common class; the default when none of the others fits.
- **`steward`** — periodic ongoing care of a surface (workspace audits, drift checks). Cadence-based, read-only, produces a structured report.
- **`gate`** — forced verdicts at decision points (pre-publish checks, code review checkpoints). Forced verdict vocabulary (PASS / PASS-WITH-NOTES / BLOCK / SKIPPED), hedged language forbidden, every finding cites evidence.
- **`utility`** — read-only helper, lightweight. Stays small on purpose.

A skill belongs to exactly one function class. Picking the right class is the most consequential design decision when building a skill — it determines the discipline the skill enforces and the output shape it produces. Documented in `docs/reference/skill-functions.md`.

---

### Strategy Template

A reusable reasoning pattern that skills compose internally during execution. Strategy templates tell the AI *how to think* about a problem of a particular shape — they enforce reasoning discipline (numbered steps, classification rules, output expectations) while leaving the content up to the skill that composes them.

- Strategy templates are AI-loaded primitives, not user-paste artifacts. (Compare to *Prompt templates*, which are user-paste.)
- A skill that does reasoning work composes one of the shipped strategies instead of reinventing the reasoning inline.
- Open Atlas v1.1 ships five: `adversarial-critique`, `chain-of-thought`, `options-and-tradeoffs`, `self-refine`, `structured-extraction`.
- Strategy templates live in `workspace/templates/strategy/`.
- The compose-don't-reinvent pattern is the load-bearing v1.1 architectural choice. It keeps skills short, makes them composable, and lets reasoning quality improve in one place (the strategy template) instead of being copy-pasted across every skill.

---

### Output Contract

A reusable output envelope that skills compose to format their output. Defines the shape, the required sections, the status vocabulary, and the rules for what goes where. The contract is the difference between "the AI gave me something useful" and "the AI gave me exactly the thing it said it would give me."

- Output contracts are AI-loaded primitives, not user-facing.
- A skill's `## Output Contract` section names the contract it composes and lists the body sections that go inside the envelope.
- Open Atlas v1.1 ships one base contract: `workspace/templates/output-contract/base.md`. v1.2 will add specialized variants (gate-output contract, steward-output contract).
- The base contract enforces: frontmatter status (one of `DONE | DONE_WITH_NOTES | BLOCKED | NEEDS_CONTEXT`), `status_why` field, summary section, optional notes section, and skill-specific body sections in the order the skill defines them.
- Output contracts live in `workspace/templates/output-contract/`.
- Documented in `docs/reference/output-contracts.md`.

---

### Compose Pattern

The load-bearing architectural principle in Open Atlas v1.1: skills compose strategy templates and output contracts instead of reinventing them inline. The same principle applies to personas (which compose voice-quality rules) and to any future primitive Open Atlas adds.

- **Why it matters:** improving the discipline once propagates to every skill that composes it. Inline-defined discipline drifts; composed discipline holds.
- **When to break the pattern:** rarely. Skills with genuinely-different reasoning needs can write their procedure inline, but should first ask whether one of the five strategies almost fits and whether the skill could compose it with a minor adaptation.
- **Where to read more:** `docs/reference/why-this-works.md` (Principle 6 — conventions ≠ enforcement); `workspace/templates/strategy/README.md`; `workspace/templates/output-contract/README.md`.

---

### Context

The input state available to an agent at activation — conversation history, loaded files, system prompt, and active persona. Context is session-bound; it does not persist across sessions.

- Context is not memory. Context is what the agent knows now; memory is what survived past sessions.
- Context has a cost (token budget). Load the minimum needed.

---

### Memory

Persisted information that survives across sessions — index files, logs, knowledge objects. Memory is cross-session by definition.

- Memory is not context. Memory must be loaded into context to be active.
- Memory has tiers: always-loaded indexes, on-demand logs, and durable knowledge objects.

---

### Governance

The authority structures, rules, and constraints that define what agents can and cannot do — who decides, what is read-only, what requires human approval.

- Governance is not a persona and not a procedure. It is the authority layer that all other concepts operate within.
- The human is the ultimate authority. Governance documents codify their decisions.

---

## Key Distinctions

| Distinction | Clarification |
| --- | --- |
| Skill vs Tool | A skill composes logic and invokes tools. A tool is atomic. |
| Skill vs Procedure | A skill executes. A procedure documents. A skill may follow a procedure's logic. |
| Persona vs Agent | One agent; many possible personas. A persona is a behavioral lens, not a separate entity. |
| Protocol vs Procedure | A protocol is binding and governs a class of events. A procedure is advisory. |
| Context vs Memory | Context is session-bound. Memory is cross-session and must be loaded to be used. |
| Template vs Procedure | A template defines structure (fill in the blanks). A procedure defines steps (follow in order). |
| Strategy Template vs Prompt Template | Strategy templates are AI-loaded internally during skill execution. Prompt templates are user-pasted into conversation. They cover similar reasoning territory at different layers. |
| Output Contract vs Document Template | Output contracts define the *envelope* for skill outputs (status, summary, notes, body sections). Document templates define the *whole structure* of a document type (e.g., a decision record). |
| Function Class vs Persona | A function class categorizes skills (gate / steward / workflow / utility). A persona shapes how an agent behaves. They are orthogonal — a steward skill can be invoked by any persona. |

---

*Part of [Open Atlas](https://github.com/tsudo/open-atlas) — a free AI workspace framework.*

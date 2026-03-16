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
│  Skill        executable, reusable workflow  │  ← composes tools + procedures
├─────────────────────────────────────────────┤
│  Tool         atomic, discrete function call │  ← building block; not composed
└─────────────────────────────────────────────┘

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

A packaged, executable, reusable workflow invoked by an agent — typically triggered by a keyword or explicit call. Skills compose tools and reference procedures to accomplish a bounded objective.

- Skills are agent-executable. They run; they do not just document.
- A skill is not a tool (atomic) and not a procedure (reference only).
- Skills have a defined trigger, an input contract, and an expected output.

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
- Two subtypes:
  - **Prompt templates** — thinking patterns with input placeholders (analysis, decision, extraction)
  - **Document templates** — document skeletons with required sections (decision records, briefs, knowledge objects)

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

---

*Part of [Open Atlas](https://github.com/tsudo/open-atlas) — a free AI workspace framework.*

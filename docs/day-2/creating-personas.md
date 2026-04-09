# Creating Personas

A persona is a named role with bounded voice, opinions, and behaviors. This doc covers when to add one, how to design one, and how to test it.

---

## When to add a persona

You should add a persona when **all three** of the following are true:

1. You find yourself asking the AI for the same *kind* of conversation more than a couple of times — adversarial review, advisory tradeoffs, classification routing, etc.
2. The default AI voice gives you outputs that are inconsistent in ways that matter — sometimes hedged, sometimes confident; sometimes opinionated, sometimes neutral.
3. You can describe what "good" looks like in concrete enough terms that you could correct a bad output by pointing at the description.

If any of those is missing, don't make a persona yet. Personas earn their place by reducing the friction of getting consistent outputs. If you can get consistent outputs by saying "give me three options with tradeoffs" once per session, you don't need a persona yet.

---

## Two ways to make one

### Option A: The kit (recommended for the first one)

Use `training/kits/persona-starter/`. The kit walks you through a structured interview:

- Pick a function shape (advisor, gate, classifier, etc.)
- Fill in voice rules
- Define what the persona does NOT do
- Generate the file
- Verify against `VERIFY.md`

The kit takes 30–45 minutes for your first persona. After that, you'll know the shape well enough to use option B.

### Option B: The single-pass prompt (for subsequent personas)

Use `training/prompts/create-persona.prompt.md`. Paste it into your AI tool with `[NAME]` and `[ONE-LINE PURPOSE]` filled in. The AI walks you through the same steps in a single conversation. Faster, less structured, requires you to know what you want.

---

## The shape of a good persona

Every persona file has these sections (enforced by `workspace/templates/persona/persona-template.md`):

| Section | What goes here |
|---|---|
| **Frontmatter** | `atlas_tier: user`, `function: [shape]`, name, voice tags |
| **Purpose** | 2–3 sentences. What the persona is for. |
| **Voice & Behaviors** | How the persona talks. Concrete rules, not vibes. |
| **What This Persona Does** | 3–7 specific things. |
| **What This Persona Does NOT Do** | At least 3 specific non-responsibilities. |
| **Activation** | How to invoke the persona. |
| **Authority Boundaries** | What it can decide alone, what it routes to you, what it never modifies. |

The "does NOT do" section is the most important and the most often skipped. A persona without explicit non-responsibilities will drift into doing everything. Force yourself to write at least three.

---

## Examples worth studying

- **`per_advisor.md`** — single-mode persona with light authority. Good first read.
- **`per_reviewer.md`** — gate-class persona with NO-HEDGING + PROOF discipline. The strongest example of how a persona enforces output quality through structure.
- **`per_librarian.md`** — two-mode persona (Classify / Route). Demonstrates how to give one persona two clearly bounded behaviors without making it a "kitchen sink" persona.
- **`per_open-atlas.md`** — the built-in default persona that ships at `workspace/personas/`. Operational rather than illustrative.

Read at least two before building your own. The shape becomes obvious after the second one.

---

## How to test a new persona

Don't ship a persona without testing it. The test:

1. **Activate the persona in a fresh session.** "Use the per_[name] persona."
2. **Give it a task in its core competency.** Does the output feel like the persona, not the default AI?
3. **Give it a task *outside* its core competency.** Does it route to you instead of trying to do the task? (If it tries, your "does NOT do" section is too thin.)
4. **Give it a task that should make it push back on you.** Does it push back, or capitulate? (If it capitulates, your voice rules are too soft.)
5. **Give it a task that should produce a hedged answer.** Does it refuse to hedge, or does it hedge anyway? (If it hedges, you need NO-HEDGING language in the persona file.)

If any of those fail, the persona file needs more constraint. Don't blame the AI — fix the file.

---

## Common failure modes

- **The persona is a vibe, not a role.** "Be more thoughtful" is not a persona. "Surface 2–4 tradeoffs and recommend one" is.
- **The persona is too broad.** A persona that handles "anything technical" is the same as the default AI. Personas earn their value by being narrow.
- **The persona has no "does NOT do" section.** It will drift. Always.
- **The persona has no authority boundaries.** It will make decisions you wanted to make yourself.
- **The persona uses hedging vocabulary** ("might want to consider," "perhaps," "seems like"). It will hedge every output. See `workspace/templates/persona/voice-quality.md` for the banned-word list.

---

## Where to go next

- **Build your first persona** → `training/kits/persona-starter/SETUP.md`
- **Build subsequent personas faster** → `training/prompts/create-persona.prompt.md`
- **Understand what makes a persona consistent** → `workspace/templates/persona/voice-quality.md`
- **Compare to skills** → [creating-skills.md](creating-skills.md). Personas are *who* the AI is being. Skills are *what* the AI is doing.

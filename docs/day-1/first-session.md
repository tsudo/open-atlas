# Your First Session

This is what your first hour with Open Atlas should feel like. Not a tutorial — a walkthrough of an actual conversation, with the things you'll think and the moves you'll make.

> **Before you start:** you should have cloned the repo, opened it in your AI tool (Claude Code, Cursor, Codex, etc.), and confirmed the AI can read files in the directory. If any of that isn't true, fix it before reading further. Open Atlas assumes the AI can see the workspace.

---

## Step 1: Tell the AI to read the workspace

Open a fresh conversation and say:

> Read `workspace/README.md` and `docs/day-1/core-concepts.md`. Then tell me in three sentences what this workspace is for and what you think I'd use it for.

This does two things at once. It loads the AI with the orientation context it needs to be useful, and it gives you a quick read on whether the AI is understanding the structure or hallucinating something adjacent.

**What good looks like:** the AI summarizes the workspace as "a governed environment for AI-assisted work, with personas, skills, and templates," names two or three concrete things you could do with it, and asks you a clarifying question about what *you* want to use it for.

**What bad looks like:** the AI lists every file it read, doesn't summarize, or starts guessing at things it didn't read. If that happens, ask it to try again and read the files first.

---

## Step 2: Capture something

Open Atlas's canonical first skill is `capture`. It exists because the most common moment you'll have with an AI workspace is "I have a thought, where does it go?"

Try it. Say:

> Read `workspace/skills/capture.md` and capture this: I want to figure out whether our team should standardize on a single AI tool or let people pick their own. The tradeoffs feel obvious in both directions and I keep going back and forth.

The AI will read the production `capture` skill, follow its procedure, and:

1. Classify the content (it's a decision-in-progress, not a finished decision)
2. Write a draft file to `workspace/drafts/` with the right frontmatter
3. Return the file path and a one-line summary in the standard output contract shape

If your AI tool supports slash commands and you've wired `/capture`, you can invoke it that way instead. Either path works — slash commands are convenience polish, reading the file by path is the canonical invocation.

The skill ships in `workspace/skills/capture.md`. You can read it yourself before invoking it; it's written to be readable.

---

## Step 3: Look at what it wrote

Open the file the AI just created. Read the frontmatter. Read the body. Notice:

- The frontmatter has all the required fields (`title`, `doc_type`, `status: draft`, `created`, `updated`, `owner`, `tags`)
- The body uses the right template structure
- Nothing has been promoted, decided, or finalized

This is the lifecycle in action. Drafts are drafts. The AI captured the thought without pretending the decision was made. Promotion to `reviewed` or `canonical` is a deliberate human move you'll learn in day 2.

---

## Step 4: Try a persona

Open Atlas ships with one persona (`per_open-atlas`) and three example personas in the kit (`per_advisor`, `per_reviewer`, `per_librarian`). For your first session, just try the built-in one. Say:

> Use the per_open-atlas persona to advise me on the AI tool standardization question I just captured.

The AI should:

- Acknowledge the persona activation
- Adopt the persona's voice (advisory, options-and-tradeoffs, no hedging)
- Reference the captured draft instead of starting from scratch
- Surface 2–4 decision-relevant tradeoffs and route to a next step

**What good looks like:** the persona feels like a different kind of conversation than "default AI." More opinionated, more structured, more obviously bounded.

**What bad looks like:** the persona is indistinguishable from the default, or it makes a recommendation without surfacing tradeoffs, or it hedges. If that happens, the persona file probably isn't being read — ask the AI to read `workspace/personas/per_open-atlas.md` explicitly and try again.

---

## Step 5: Notice what you didn't have to do

Stop and look back at what the last 20 minutes required from you:

- You did not configure anything
- You did not wire any slash commands
- You did not write any frontmatter
- You did not create any directories
- You did not run any scripts

You ran Open Atlas with conventions and a few `read this file` instructions to the AI. That's the minimum-viable-adoption path. It works because the workspace is designed to make the right moves obvious to the AI, not because of any magic infrastructure.

When you want more — slash commands, hooks, custom skills, custom personas — that's day 2. But day 2 is optional. Plenty of people will live in this minimum-viable mode forever and get real value out of it.

---

## What to do next

- **Want to understand what you just used?** → [core-concepts.md](core-concepts.md)
- **Want to build something of your own?** → start with [../day-2/creating-personas.md](../day-2/creating-personas.md) or [../day-2/creating-skills.md](../day-2/creating-skills.md)
- **Want to know whether Open Atlas is the right shape for your team?** → [../reference/why-this-works.md](../reference/why-this-works.md)

---

## You're ready for day 2 when

You've felt the gap. Specifically:

- You've run a full session in the workspace
- You've used at least one persona or skill (or asked the AI to read one)
- You've caught yourself thinking *"I wish the AI just knew this without me telling it"*

If you haven't felt the gap, you don't need day 2 yet. Come back when you have. The minimum-viable adoption path is the right place to live until then.

---

## When something feels off

- **The AI is ignoring the workspace structure** → it probably hasn't read the orientation files. Ask it to read `workspace/README.md` explicitly.
- **A skill or persona didn't activate** → ask the AI to read the file directly. Skill/persona auto-activation depends on your AI tool's wiring; it may or may not work without explicit reads.
- **The AI wrote a file to a weird location** → tell it where it should have gone, and consider whether `workspace/README.md` makes the right location obvious. If not, that's a doc gap worth fixing.
- **Something feels too magical** → it probably is. Open Atlas is not magic. If you can't explain why the AI did something, ask it. The AI should be able to point at the file or convention that drove the move.

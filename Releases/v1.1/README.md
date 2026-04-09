# Open Atlas v1.1

**Released:** 2026-04-07

---

## What you get in v1.1

v1.0 gave you a workspace structure. v1.1 gives you everything you need to *use* it — and to teach a colleague to use it without your help.

**Build your own personas.** Start with three working examples (`per_advisor`, `per_reviewer`, `per_librarian`), a hardened template, and either a structured starter kit or a single-pass prompt depending on how much guidance you want. Each example demonstrates a different shape — single-mode advisor, gate-class reviewer with forced verdicts, two-mode classifier — so you can see what's possible before you write your own.

**Run four production skills out of the box.** v1.1 ships `capture` (turn a thought into a draft), `think-it-through` (decision help that refuses to tell you what you want to hear, with a flip-the-question gate that forces honest recommendations), `extract-knowledge` (turn an insight into a durable workspace artifact), and `review-context` (weekly workspace audit). Together they form a complete loop: capture in → think things through → extract what's durable → maintain the workspace. Each skill is in `workspace/skills/`, ready to invoke from your first session.

**Build your own skills.** A hardened template, four function classes to pick from (gate, steward, workflow, utility), five reasoning strategies your skills can compose (`adversarial-critique`, `chain-of-thought`, `options-and-tradeoffs`, `self-refine`, `structured-extraction`), and a base output contract every skill inherits. Copy `workspace/skills/my-next-skill.md`, rename it, fill in the sections — or run the starter kit interview at `training/kits/skill-starter/SETUP.md`. The discipline ships with the template; you focus on the procedure.

**Documentation that respects your time.** Three layers: day-1 if you just installed an AI tool, day-2 if you're building things for your team, reference if you want to understand why the conventions are the way they are. Each layer answers the question *"is this for you yet?"* honestly. The minimum-viable adoption path is two concepts and zero infrastructure.

**See the difference.** The new `examples/before-after.md` shows the same prompt asked of an AI working in a junk-drawer directory vs. an AI working in an Open Atlas workspace. The difference is not subtle.

---

## What changed if you used v1.0

- **`workspace/skills/` now ships with content.** v1.0 shipped this directory empty (placeholder README only). v1.1 ships four production skills (`capture`, `think-it-through`, `extract-knowledge`, `review-context`) and a starter scaffold (`my-next-skill`). These are framework-tier files; if you'd added your own skills to this directory in v1.0, they're untouched (user-tier always wins). The new files appear alongside yours.
- **Knowledge object schema narrowed.** Decisions, project briefs, meeting notes, playbooks, and recipes are no longer KNO doc types. They still exist as documents — they just aren't durable knowledge in the new sense. If you have files with these types, they keep working; they don't fit the new "what counts as a KNO" rubric.
- **`docs/why-this-works.md` moved** to `docs/reference/why-this-works.md`. Update bookmarks.
- **New file ownership manifest** (`.atlas-manifest.yml`) declares which files Open Atlas owns vs. which files you own — so future upgrades can leave your work alone. The manifest model ships in v1.1; the upgrade tool ships in v1.2. For now, upgrades are manual.

Everything else is additive. v1.0 → v1.1 is safe: nothing was removed.

---

## What v1.1 deliberately does not include

- **No upgrade tool yet.** The manifest model is shipped; the script that uses it is v1.2.
- **No working hooks.** v1.1 ships hook *designs* (as explainers in `hooks/`); working scripts are v1.2 with a test harness. If you want to wire your own hooks now, the explainers tell you what to enforce and why.
- **No test harness for skills or personas.** Verification is human-in-the-loop via the `VERIFY.md` files in each kit.
- **No PowerShell port of the future hook scripts.** Bash only; PRs welcome when v1.2 ships them.
- **No vector store, no agent orchestration runtime, no telemetry.** Out of scope by design.

---

## What's next

The v1.2 list is open. If you'd like to influence it, open an issue.

---

*Open Atlas — CC-BY-4.0. See [LICENSE](../../LICENSE) for terms.*

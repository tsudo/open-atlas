# Project Pipeline

A structured path from idea to shipped artifact. Six phases, four enforcement gates, and size-gating that scales the ceremony to match the work.

---

## Why a pipeline

AI workspaces make it easy to start building and hard to finish well. The failure mode isn't "bad code" — it's "forgot to update the README," "shipped without checking cross-references," "left three follow-up tasks in session notes that disappeared." These are process failures, not competence failures, and they share one root cause: secondary constraints drop under cognitive load.

A pipeline doesn't eliminate cognitive load. It moves the important checks from "remember to do this" to "the process requires this before you can proceed." The difference is reliability — [Tier 0.5 vs Tier 3](../reference/reliability-tiers.md) on the enforcement spectrum.

---

## The six phases

Every project passes through these phases. Size-gating determines which phases are required.

### 1. Propose

Establish what you're building, how big it is, and what template governs the output.

- **Classify the project type.** What kind of work is this? A new feature, an upgrade, a content release, an infrastructure change? The type determines which reviewers and checks apply.
- **Determine size.** S (single session, well-defined), M (multi-step, needs review), or L (scoped initiative, needs spec + adversarial review). When in doubt, start at M.
- **Name the governing template.** Every durable artifact should reference the template it was created from. This is the first enforcement gate (G1) — if you can't name the template, the scope isn't clear enough.

**Output:** Project type, size, governing template, reviewer roles.

### 2. Spec (M and L only)

Define what "done" looks like before you start building.

- **Scope definition.** What's in, what's out, what's deferred.
- **Acceptance criteria.** Specific, testable conditions. Not "it works" — "all cross-references resolve, frontmatter is valid, README reflects current state."
- **Dependencies.** What must exist before this can start? What does this block?
- **Exit criteria per phase.** What does "done" look like for Build, Review, and Ship individually?

S-sized tasks skip this phase. The Propose output is enough scope for single-session work.

**Output:** Spec document or section. User approves before Build begins.

### 3. Build

Do the work. The only enforcement here is a G1 re-check: the first artifact you produce must reference the governing template named in Propose. This catches the "started building before loading the right template" failure, which is more common than it sounds.

**Output:** Built artifacts. List of what was produced.

### 4. Review (M and L only)

Check the work before shipping.

- **Cross-reference verification (G2).** Every internal link in every built artifact must resolve to a real file. This is a hard gate — the pipeline pauses until all links resolve. This is the single most common failure mode in workspace releases. Check mechanically, not by skimming.
- **Multi-reviewer pass.** For each reviewer role identified in Propose, apply that lens to the artifact set. A brand reviewer checks voice and tone. A security reviewer checks for leaked credentials. A technical reviewer checks for correctness.
- **L only: Adversarial red-team.** An independent review (ideally by a different AI model or a fresh session with no build context) stress-tests the artifacts for blind spots the builder missed.

**Output:** Findings with severity. All HIGH findings resolved before Ship.

### 5. Ship

Release the work.

Four gates fire at Ship:

| Gate | What it checks | Severity |
|---|---|---|
| **G2 re-run** | Cross-references still resolve (nothing broke during review fixes) | Hard — pauses on failure |
| **G3** | Project README is current (updated date, accurate status) | Hard — no "shipped" announcement with a stale README |
| **G4** | No deferred work stuck in session-scoped notes | Soft — surfaces items for promotion to a durable task tracker |

After gates pass: commit, tag, push, deploy, release — whatever "ship" means for your project.

**Output:** Ship artifact (commit hash, tag, release URL).

### 6. Verify (M and L only)

Confirm the shipped artifact works in the real world.

- **G4 re-run.** Final check for deferred work that slipped through.
- **Live-state verification.** If the artifact is public (GitHub repo, deployed site), fetch it and confirm it renders correctly.
- **Capture failures.** Anything that broke goes into the backlog, not into a "we'll fix it later" note that disappears.

**Output:** Verification result. Pipeline closes.

---

## Size-gating

Not every project needs every phase. Size determines the minimum ceremony.

| Size | Phases | When to use |
|---|---|---|
| **S** | Propose → Build → Ship | Single-session, well-defined tasks. A bug fix, a small doc update, a one-file addition. |
| **M** | Propose → Build → Review → Ship | Multi-step work. New features, template changes, anything touching multiple files. |
| **L** | Propose → Spec → Build → Review → Ship → Verify | Scoped initiatives. Releases, architectural changes, cross-cutting refactors. |

**Reclassification:** If a task grows mid-build, pause and re-run the skipped phases. Don't push an S-sized task through Ship without Review just because you started at S. Once Build begins, you can upgrade size but not downgrade — that prevents "do the hard work at L, downgrade to skip Review."

---

## The four gates

Gates are enforcement checkpoints that fire at phase boundaries. They exist because the checks they perform are the ones most commonly skipped under cognitive load.

| Gate | When | What | Why |
|---|---|---|---|
| **G1** | Propose + Build | Correct template identified and referenced | Prevents building from the wrong template or no template |
| **G2** | Review + Ship | All internal links resolve | Catches the single most common release failure: broken cross-references |
| **G3** | Ship | Project README is current | Prevents shipping with a stale README (the README is operational state, not documentation) |
| **G4** | Ship + Verify | No deferred work in session-only storage | Prevents losing follow-up tasks when the session ends |

G1, G2, and G3 are hard gates — the pipeline pauses until the issue is fixed. G4 is a soft gate — it surfaces items for your decision but doesn't block ship.

---

## Customizing the pipeline

The pipeline contract at [`workspace/governance/pipeline.md`](../../workspace/governance/pipeline.md) defines the phases, gates, and size rules for your workspace. The defaults match this guide. You can:

- **Add project types** with different reviewer roles and type-specific rules
- **Add gates** for checks specific to your workflow (e.g., a license-header check for open-source projects)
- **Adjust size thresholds** — if your "small" tasks routinely need review, redefine S to include Review

The contract is a governance artifact, not code. Your AI reads it and follows the procedure. The gates are enforced by the AI following the skill's instructions, not by automated tooling (though hooks can add mechanical enforcement at [Tier 5](../reference/reliability-tiers.md)).

---

## Connection to hooks

The pipeline gates described here operate at [Tier 3](../reference/reliability-tiers.md) — they fire when the pipeline skill is invoked. For Tier 5 enforcement, you can implement gates as hooks that fire on tool events. See [`hooks/`](../../hooks/) for design notes on hook-based enforcement. A natural first hook for pipelines: a bypass detector that flags ship-class commands (git push, deploy) executed outside a pipeline run.

---

*Open Atlas v1.2. See [reliability-tiers.md](../reference/reliability-tiers.md) for the enforcement spectrum, and [skill-functions.md](../reference/skill-functions.md) for how pipeline skills fit into the function classification.*

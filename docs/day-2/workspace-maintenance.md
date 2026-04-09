# Workspace Maintenance

Workspaces accumulate. Drafts pile up unread. Frontmatter drifts from the schema. Knowledge objects get stale. This doc covers the cadence and the moves that keep the workspace from rotting.

---

## The lifecycle

Every durable file in Open Atlas has a status:

| Status | Meaning |
|---|---|
| `draft` | Just captured. Not reviewed by you. Not load-bearing. |
| `reviewed` | You've read it. It's coherent. It's not yet canonical. |
| `canonical` | You've decided this is the source of truth. Other things can depend on it. |
| `archived` | No longer current. Kept for history. Should not be referenced as canonical. |

The lifecycle is one-directional in practice — most files go `draft → reviewed → canonical → archived`, though some files start at `reviewed` if you're writing them deliberately rather than capturing them.

**Promotion is always a human decision.** Skills can flag candidates. Skills cannot promote.

---

## The maintenance cadence

Pick a cadence and stick to it. The two that work in practice:

### Weekly (recommended)

Once a week, run a workspace audit. The `review-context` example skill is built for this. It walks the workspace, flags drift, and produces a report. The whole thing takes 5–15 minutes including your read.

**What to do with the report:**

- **Drafts older than 30 days** → promote, archive, or delete. If it's been a draft for a month and you haven't touched it, it's not going to become canonical on its own.
- **Frontmatter validation failures** → fix them in place. Drift compounds.
- **Files in the wrong directory** → move them. Predictability is the whole point.
- **Schema mismatches** → either fix the file or, if the schema is wrong, fix the schema (and write down why).

### After every meaningful work session

Lighter touch. After a session that produced new files, scan the new files: did they land in the right place, with the right frontmatter? If yes, you're done. If no, fix it now while the context is fresh.

This catches problems that accumulate. Weekly audits catch problems that have already accumulated.

---

## What "drift" looks like

| Drift type | What it looks like | What to do |
|---|---|---|
| **Stale drafts** | `workspace/drafts/` has files older than 30 days you haven't touched | Promote, archive, or delete |
| **Frontmatter rot** | A file has fields that aren't in the schema, or is missing required fields | Fix the file, or update the schema and document why |
| **Wrong-directory files** | A KNO is in `drafts/`, a draft is in `knowledge/` | Move it. Don't make exceptions for "this one is special." |
| **Stale canonicals** | A `canonical` file references things that no longer exist | Update it or demote it to `reviewed` |
| **Orphaned files** | Files nothing references and you don't remember writing | Read them. If still valuable: tag and route. If not: archive. |

---

## When to archive

Archive a file when:

- You've stopped referencing it
- The context that made it useful no longer applies
- A newer file has superseded it
- It was useful at the time but you wouldn't write it again today

Archived files are not deleted. They live in `workspace/archive/` (or wherever your workspace puts them) with `status: archived`. Future-you might want to read them; future-AI should not treat them as load-bearing.

---

## When to delete

Delete a file when:

- It's a duplicate
- It was a typo or accidental capture
- It contains content that should not exist anywhere (a credential that leaked into a draft, etc.)

Most files should be archived, not deleted. Deletion is for mistakes and contamination.

---

## When to refactor the workspace itself

If you find yourself adding the same kind of file in the same kind of place over and over, that's a signal. You've discovered a pattern. Encode it:

- **Add a directory** for the new pattern
- **Add a template** to `workspace/templates/`
- **Update `workspace/README.md`** so the pattern is discoverable
- **Maybe add a skill** if the workflow is multi-step

This is how the workspace evolves to fit your work. The shipped structure is a starting point, not a constraint.

---

## What not to maintain

You can spend infinite time maintaining a workspace. Don't.

- **Don't reorganize for the sake of reorganizing.** If the structure works, leave it.
- **Don't promote drafts that aren't ready.** The lifecycle is meaningful only if `canonical` actually means canonical.
- **Don't add fields to the schema for one-off needs.** Use tags or notes instead.
- **Don't audit daily.** Weekly is plenty for most workspaces.
- **Don't delete things you might want to read in six months.** Archive them.

---

## Where to go next

- **The audit skill** → [`workspace/skills/review-context.md`](../../workspace/skills/review-context.md)
- **The promotion discipline** → [../reference/feedback-loop.md](../reference/feedback-loop.md)
- **The schemas you're auditing against** → `workspace/templates/schema/`

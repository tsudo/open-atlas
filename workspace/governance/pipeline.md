# Pipeline Contract

Defines the phases, gates, and size rules for project work in this workspace. Your AI reads this file and follows the procedure when a pipeline skill is invoked.

Edit this file to match your workflow. The defaults below work for most workspaces.

---

## Phases

| Phase | Order | Required for | Purpose |
|---|---|---|---|
| **Propose** | 1 | S, M, L | Establish scope, size, governing template |
| **Spec** | 2 | M, L | Define acceptance criteria and exit conditions |
| **Build** | 3 | S, M, L | Do the work |
| **Review** | 4 | M, L | Check the work (cross-refs, multi-reviewer, red-team for L) |
| **Ship** | 5 | S, M, L | Release the work (all gates fire here) |
| **Verify** | 6 | M, L | Confirm the shipped artifact works |

S-sized tasks take the fast path: Propose → Build → Ship.

---

## Size Gating

| Size | When to use | Phases |
|---|---|---|
| **S** | Single session, well-defined. Bug fix, small doc update. | Propose, Build, Ship |
| **M** | Multi-step. New feature, template change, multi-file work. | Propose, Build, Review, Ship |
| **L** | Scoped initiative. Release, architectural change, refactor. | All six phases |

**Reclassification rules:**
- Upgrade anytime (S→M, M→L). Pause and run skipped phases.
- Downgrade only before Build starts. Once Build begins, size is locked.

---

## Gates

| Gate | Fires at | Severity | What it checks |
|---|---|---|---|
| **G1** | Propose, Build | Hard | Governing template identified and referenced in first artifact |
| **G2** | Review, Ship | Hard | All internal markdown links resolve to real files |
| **G3** | Ship | Hard | Project README has current date and accurate status |
| **G4** | Ship, Verify | Soft | No deferred work stuck in session-only notes |

**Hard gates** pause the pipeline until the issue is fixed.
**Soft gates** surface items for your decision but don't block ship.

---

## Project Types

Add your own project types here. Each type can define reviewer roles and type-specific rules.

```
# Example project types — customize for your workspace

default:
  reviewer_roles: []
  rules: []

content-release:
  reviewer_roles: [brand, technical]
  rules:
    - "Attribution check at Ship"
    - "Voice review before publish"

infrastructure:
  reviewer_roles: [technical, security]
  rules:
    - "Backup before Build"
    - "Rollback plan documented in Spec"
```

---

## How to Use This File

1. When starting a project, your AI reads this contract to determine the right phases and gates.
2. At each gate, the AI runs the specified check and reports the result.
3. Hard gate failures pause the pipeline. Fix the issue, re-run the gate, continue.
4. Soft gate warnings are surfaced for your decision.

The contract is governance, not automation. Your AI follows it because the pipeline skill's procedure tells it to. For mechanical enforcement, implement gates as hooks (see `docs/reference/reliability-tiers.md` for the Tier 3 → Tier 5 upgrade path).

---

*Open Atlas v1.2. See [docs/day-2/project-pipeline.md](../../docs/day-2/project-pipeline.md) for the full guide.*

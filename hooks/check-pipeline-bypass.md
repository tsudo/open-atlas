# Hook Design: Pipeline Bypass Detection

Detects ship-class commands executed without an active pipeline run.

---

## What it catches

When someone runs a ship-class command — `git push`, a deploy command, a release command — this hook checks whether an active pipeline run exists. If no pipeline is in progress, it flags the action. The flag is informational, not blocking: it surfaces the fact that a ship action happened outside the pipeline's discipline.

Ship actions without pipeline coverage aren't always wrong. Emergency hotfixes, one-line typo fixes, and exploratory pushes to non-production branches are legitimate bypasses. The hook's value is making bypasses *visible* rather than *silent* — so the operator can decide whether the bypass was intentional or a missed step.

---

## How it works

**Trigger:** PostToolUse on Bash (fires after a shell command runs).

**Match commands:** Any command containing `git push`, `gh release`, or your deploy command (e.g., `npx wrangler deploy`, `vercel deploy`, or whatever ships your project).

**Check:** Does the pipeline run log contain a `PIPELINE_START` event for the current session without a corresponding `PIPELINE_COMPLETE`? If no active run exists, the hook fires.

**Action:** Flag only. The hook prints a warning to stderr and optionally logs a `PIPELINE_BYPASS_WARN` event. It does not block the command. The operator decides how to respond.

---

## Implementation sketch

```bash
#!/usr/bin/env bash
# check-pipeline-bypass.sh
# PostToolUse hook — fires after Bash commands
# Mode: flag-only (exit 0 always)

COMMAND="$1"

# Match ship-class commands
if [[ "$COMMAND" =~ "git push" ]] || \
   [[ "$COMMAND" =~ "gh release" ]] || \
   [[ "$COMMAND" =~ "wrangler deploy" ]]; then

  # Check for active pipeline run
  # (Implementation depends on your pipeline run log format)
  # Example: grep for PIPELINE_START without matching PIPELINE_COMPLETE
  
  ACTIVE_RUN=$(check_for_active_pipeline_run)
  
  if [[ -z "$ACTIVE_RUN" ]]; then
    echo "⚠ Ship-class command detected outside pipeline run." >&2
    echo "  Command: $COMMAND" >&2
    echo "  This may be intentional (hotfix, exploratory push)." >&2
    echo "  If not, consider running through the pipeline first." >&2
    # Optionally log the bypass event
  fi
fi

exit 0  # Never block — flag only
```

The `check_for_active_pipeline_run` function depends on how your workspace tracks pipeline state. If you use the pipeline contract from `workspace/governance/pipeline.md`, you might maintain a simple state file or log that records when a pipeline starts and completes.

---

## Design decisions

**Flag-only, not blocking.** A blocking hook would prevent emergency hotfixes and create a "disable the hook to ship" anti-pattern. Flag-only preserves the operator's authority while making bypasses visible.

**PostToolUse, not PreToolUse.** The command has already run by the time the hook fires. This is intentional — the goal is awareness, not prevention. A PreToolUse hook would need to predict whether the command *would* ship something, which is fragile.

**Match commands are configurable.** Different projects ship differently. The hook should match whatever "ship" means for your project. Start with `git push` and your deploy command; add more as you discover patterns.

---

## When to implement this

This hook is worth implementing when you've established a pipeline discipline and want to detect drift — commands that bypass the pipeline without intention. If you're still building your pipeline, skip this hook and come back to it once the pipeline is part of your workflow.

On the [reliability tier](../docs/reference/reliability-tiers.md) spectrum, this hook operates at Tier 5 — it fires mechanically on every matching command, regardless of what the AI is thinking about. That makes it the strongest enforcement surface for pipeline discipline.

---

## Relationship to other hooks

| Hook | What it detects | When it fires |
|---|---|---|
| `check-credential-leak` | Secrets in tool I/O | Pre/PostToolUse |
| `check-injection-egress` | Prompt injection patterns | Pre/PostToolUse |
| `check-pre-action` | Writes without loading governance context | PreToolUse |
| **`check-pipeline-bypass`** | **Ship commands without active pipeline** | **PostToolUse** |

---

*Open Atlas v1.2. See [docs/day-2/project-pipeline.md](../docs/day-2/project-pipeline.md) for the pipeline pattern this hook enforces, and [docs/reference/reliability-tiers.md](../docs/reference/reliability-tiers.md) for how hooks fit into the enforcement spectrum.*

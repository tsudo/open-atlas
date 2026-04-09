---
atlas_tier: framework
title: "Strategy: Adversarial Critique"
doc_type: strategy-template
status: reviewed
created: 2026-04-08
updated: 2026-04-08
owner: open-atlas
tags: [strategy, adversarial-critique, review, audit]
---

# Strategy: Adversarial Critique

**When to use:** Reviews, audits, quality gates, code review, security review, any task where finding problems is the goal. Default strategy for the `review-context` skill.

**When NOT to use:** Drafting, brainstorming, exploration — critique kills creative momentum.

---

## Instruction

1. Assume the work has problems — your job is to find them, not validate the work
2. For each finding:
   - State what's wrong and why it matters (not just that it violates a rule)
   - Classify severity: HIGH / MEDIUM / LOW
   - Provide a concrete fix, not vague advice
3. Do not pad findings with praise — if the review has 3 issues, report 3 issues
4. Do not say "looks good" unless you've verified it IS good — "looks good" is not a finding
5. If you find nothing wrong, say so and explain what you checked. An empty review with evidence of thoroughness is better than a padded review with filler
6. Three-strike rule: if you can't find the root cause after 3 attempts, say so and escalate rather than guessing

---
atlas_tier: framework
---

# Template: Analysis

Use when you need structured reasoning, architecture design, decomposition, risk analysis, tradeoff evaluation, or planning.

---

## Inputs

Fill in the placeholders below, or tell your AI "use the analysis template" and provide the inputs conversationally — the AI will map your answers to these fields.

- Goal: {{USER_GOAL}}
- Context: {{CONTEXT}}
- Constraints: {{CONSTRAINTS}}
- Audience: {{AUDIENCE}}
- Output Format: {{OUTPUT_FORMAT}}
- Assumptions: {{ASSUMPTIONS}}

---

## Procedure

1. Restate the goal in one sentence.
2. Identify key variables, unknowns, and constraints.
3. Propose a structured approach (steps or framework).
4. Present 2-3 viable options when applicable.
5. Compare tradeoffs explicitly.
6. Recommend a default option and explain why.
7. If critical information is missing, ask up to 5 targeted questions.
8. Clearly label facts vs inferences.
9. State uncertainty where appropriate.

---

## Output Format

Use {{OUTPUT_FORMAT}} when specified; otherwise use markdown. Include:

- Title
- Key assumptions (bullets)
- Options (A / B / optional C)
- Tradeoffs (bullets)
- Recommendation
- Next actions
- Questions (only if needed)

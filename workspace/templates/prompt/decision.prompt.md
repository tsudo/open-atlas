---
atlas_tier: framework
---

# Template: Decision Support

Use when you need a recommendation between choices, prioritization, a go/no-go decision, or risk-based selection.

---

## Inputs

Fill in the placeholders below, or tell your AI "use the decision template" and provide the inputs conversationally.

- Decision to make: {{USER_GOAL}}
- Options: {{CONTEXT}}
- Constraints: {{CONSTRAINTS}}
- Audience: {{AUDIENCE}}
- Output Format: {{OUTPUT_FORMAT}}
- Assumptions: {{ASSUMPTIONS}}

---

## Procedure

1. Define the decision and success criteria.
2. List options and evaluate them against criteria.
3. Surface major risks and mitigations for each option.
4. Provide a recommendation with a confidence rating: High / Medium / Low.
5. If information is missing, ask only the minimum questions required to decide.

---

## Output Format

Use {{OUTPUT_FORMAT}} when specified; otherwise use markdown. Include:

- Decision statement
- Criteria (bullets)
- Option evaluation table (if useful)
- Recommendation + confidence
- Risks + mitigations
- Next step

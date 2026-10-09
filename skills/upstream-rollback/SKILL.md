---
name: upstream-rollback
description: Record and escalate defects in requirements or design discovered downstream; obtain a decision before changing the source of truth.
---

# upstream-rollback

Use this skill in Design, Engineering, and QA when evidence shows that a requirement or approved design is incorrect, incomplete, contradictory, unsafe, or not implementable as written.

## Required process

1. Stop work that depends on the disputed behavior. Continue only independent work that cannot bake in the disputed assumption.
2. Do not silently alter requirements, a plan, a design, or tests to hide the conflict. Do not implement a different behavior while awaiting a decision.
3. Record one issue at `agents/<feature-name>/problems/<feature-name>.problem.json`. Create the feature and `problems/` directories if needed. If one file already exists, append the new issue to its `issues` array while preserving prior decisions and history.
4. Ask the user for a decision in chat. Include the issue ID and a concise summary of the evidence, impact, and viable options. Wait for the decision before changing the source of truth or dependent implementation/tests.
5. After the decision, record it in the problem artifact, update the affected plan/design through its owner, and resume downstream work from the approved version.

## Problem artifact format

The file must be valid JSON with this shape:

```json
{
  "schema_version": 1,
  "artifact_type": "upstream_problem_log",
  "feature": "payment-integration",
  "issues": [
    {
      "id": "UP-001",
      "discovered_by": "engineering",
      "block_id": "a",
      "source": {"type": "design", "path": "agents/features/payment-integration/design/payment-integration-a.design.json"},
      "summary": "The design expects a provider response field that the selected SDK does not expose.",
      "evidence": ["The installed SDK type definition has no client_secret field on this response."],
      "impact": "The approved response mapping cannot be implemented as specified.",
      "options": ["Return the provider's supported confirmation token", "Revise the provider integration"],
      "status": "awaiting_decision",
      "decision": null,
      "history": []
    }
  ]
}
```

Use stable unique IDs (`UP-001`, `UP-002`, ...). `source.type` is `requirements`, `plan`, or `design`. When a decision is made, set `status` to `resolved`, record the user's decision, rationale, and date in `decision`, and append a dated entry to `history`. Never overwrite earlier issue history. Do not place secrets or unnecessary personal data in the log.

## Decision record

```json
{
  "decision": {
    "outcome": "Revise the response contract to use the supported confirmation token.",
    "rationale": "The installed provider SDK does not return the planned field.",
    "decided_by": "user",
    "decided_at": "YYYY-MM-DD"
  }
}
```

The example date is a placeholder; use the actual decision date. Until the user decides, keep the issue at `awaiting_decision` and do not modify the affected source of truth.

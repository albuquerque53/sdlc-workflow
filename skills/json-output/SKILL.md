---
name: json-output
description: Create compact, consistent JSON plan and design artifacts for the SDLC agents while keeping exploratory reasoning and clarification in chat.
---

# json-output

Use this skill for Planning and Design artifacts. JSON is the finalized handoff format, not a replacement for reasoning or user conversation. Resolve ambiguity and decisions in chat first, then serialize the result. Prefer concise, self-descriptive keys and shallow structures: do not save tokens by using cryptic abbreviations or omitting information needed by the next agent.

## Rules

- Emit exactly one valid JSON object per artifact; no Markdown fences, comments, trailing commas, or prose in the file.
- Use UTF-8 and two-space indentation. Use `snake_case` keys and descriptive strings.
- Keep only information needed for implementation, review, and traceability. Avoid repeating feature-level context in every block.
- Use `[]` for known-empty lists. Use `null` only when a field is genuinely unknown or not decided; explain it in `open_questions` when material.
- Preserve user-approved decisions and cite repository-relative paths. Do not invent facts to complete a schema.
- Validate syntax and required fields before handing off. If the environment offers structured-output/schema enforcement, use it.
- Keep free-form reasoning, requirement discovery, and user questions in chat. JSON structure should constrain the final artifact without constraining the reasoning process.

## Plan artifact

Path: `agents/features/<feature-name>/plans/<feature-name>-<block-id>.plan.json`

Required shape (field names are the contract):

```json
{
  "schema_version": 1,
  "artifact_type": "feature_block_plan",
  "feature": "payment-integration",
  "block": {
    "id": "a",
    "title": "Create payment intent",
    "goal": "Create a payment intent and return its client secret."
  },
  "requirements": ["A valid request creates one payment intent."],
  "codebase_context": [
    {"path": "src/payments/service.ts", "relevance": "Existing payment service entry point."}
  ],
  "scope": {"in": ["Create intent"], "out": ["Capture payment"]},
  "dependencies": [],
  "acceptance_criteria": ["The API returns the intent identifier and client secret."],
  "test_scenarios": [
    {"id": "happy_path", "given": "Valid payment details", "when": "An intent is requested", "then": "An intent is created and returned."}
  ],
  "risks": [],
  "assumptions": [],
  "open_questions": []
}
```

## Design artifact

Path: `agents/features/<feature-name>/design/<feature-name>-<block-id>.design.json`

Required shape:

```json
{
  "schema_version": 1,
  "artifact_type": "feature_block_design",
  "feature": "payment-integration",
  "block_id": "a",
  "plan_path": "agents/features/payment-integration/plans/payment-integration-a.plan.json",
  "decisions": [
    {"topic": "Payment client", "decision": "Use the existing provider SDK", "approved_by": "user"}
  ],
  "components": [
    {"name": "PaymentService", "path": "src/payments/service.ts", "change": "Add intent creation", "behavior": ["Validate amount", "Return provider intent"]}
  ],
  "interfaces": [],
  "data_flow": ["Request -> PaymentService -> Provider -> Response"],
  "error_handling": [],
  "compatibility": [],
  "test_scenarios": ["Valid request returns an intent", "Provider error is surfaced safely"],
  "open_questions": []
}
```

The examples show field types and level of detail, not mandatory domain values. Do not include empty optional sections if they do not apply; keep required fields present. Feature names in paths use lowercase kebab-case, and block IDs use stable lowercase letters (`a`, `b`, `c`) or an equally clear stable identifier.

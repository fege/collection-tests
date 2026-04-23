---
test_case_id: TC-META-003
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-003: Verify servingConfig absent from API response for model without validated config

**Objective**: Confirm that models without validated tool calling configurations do not expose `servingConfig` in the API response.

**Preconditions**:
- AI Hub catalog service deployed
- At least one model loaded from `metadata.yaml` without a `servingConfig` block

**Test Steps**:
1. Retrieve a model without tool calling config: `GET /api/catalog/models/{modelId}` (e.g., `granite-7b-redhat-lab`)
2. Parse the JSON response
3. Verify the `servingConfig` field is entirely absent (not present as `null` or empty object)
4. Verify the model's `tasks` array does NOT include `tool-calling`

**Expected Results**:
- `servingConfig` key is not present in the JSON response
- `tasks` array contains only applicable tasks (e.g., `["text-generation"]`)
- No tool calling configuration data is exposed for unvalidated models

**Notes**: To be filled later in the process.

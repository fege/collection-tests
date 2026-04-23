---
test_case_id: TC-META-007
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-007: Verify tasks array includes tool-calling when servingConfig.toolCalling is present

**Objective**: Confirm that models with `servingConfig.toolCalling` have `tool-calling` in their `tasks` array for capability discovery via `filterQuery`.

**Preconditions**:
- AI Hub catalog service deployed with models loaded from `metadata.yaml`

**Test Steps**:
1. Retrieve a model with validated tool calling config: `GET /api/catalog/models/{modelId}`
2. Verify the `tasks` array includes `"tool-calling"`
3. Retrieve a model without tool calling config
4. Verify its `tasks` array does NOT include `"tool-calling"`
5. Use `filterQuery` parameter with `tasks=tool-calling` on the list endpoint: `GET /api/catalog/models?filterQuery=tasks:tool-calling`
6. Verify the response contains only models with `tool-calling` in their `tasks` array
7. Verify each returned model also has `servingConfig.toolCalling` present

**Expected Results**:
- `tool-calling` task is present for models with `servingConfig.toolCalling`
- `filterQuery` correctly filters to only tool-calling-capable models
- No model has `tool-calling` in `tasks` without a corresponding `servingConfig.toolCalling` block

**Notes**: To be filled later in the process.

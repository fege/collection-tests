---
test_case_id: TC-NEG-001
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-001: Model without servingConfig does not expose tool calling configuration via API

**Objective**: Confirm that models without a `servingConfig.toolCalling` block in CatalogModel do not expose any tool calling configuration data through the catalog API.

**Test Steps**:
1. Identify a model in the catalog that does NOT have `servingConfig.toolCalling` populated
2. Query the catalog API for that model:
   ```
   GET /api/v1/models/<model-id-without-servingConfig>
   ```
3. Inspect the API response for any `servingConfig` or `toolCalling` fields
4. Verify that no tool calling configuration data is present in the response

**Expected Results**:
- The API response does not contain `servingConfig.toolCalling` or any of its sub-fields (`toolCallParser`, `chatTemplate`, `enableAutoToolChoice`, `requiredArgs`)
- The `servingConfig` field is either absent or does not contain a `toolCalling` property
- No partial or placeholder tool calling configuration is exposed

**Notes**: To be filled later in the process.

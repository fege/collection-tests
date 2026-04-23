---
test_case_id: TC-NEG-005
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-005: Partial servingConfig (toolCallParser present but chatTemplate missing) is not published

**Objective**: Confirm that a model with an incomplete `servingConfig.toolCalling` (e.g., `toolCallParser` present but `chatTemplate` missing) is not published to the catalog API with partial tool calling configuration.

**Preconditions**:
- Access to the model metadata ingestion pipeline or database

**Test Steps**:
1. Prepare a model metadata entry with partial `servingConfig.toolCalling`:
   - Set `toolCallParser` to a valid value
   - Omit `chatTemplate` entirely
   - Set `enableAutoToolChoice` to `true`
2. Attempt to ingest or publish this model metadata through the normal pipeline
3. Query the catalog API for this model:
   ```
   GET /api/v1/models/<model-id>
   ```
4. Inspect the response for `servingConfig.toolCalling`

**Expected Results**:
- Either the ingestion pipeline rejects the partial configuration with a validation error, OR
- The catalog API does not expose the partial `servingConfig.toolCalling` to consumers
- Users are never presented with an incomplete tool calling configuration that would fail at deployment time

**Notes**: To be filled later in the process.

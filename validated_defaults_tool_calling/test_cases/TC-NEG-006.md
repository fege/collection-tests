---
test_case_id: TC-NEG-006
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-006: Malformed serving_config JSON in database handled gracefully by mapDBModelToAPIModel()

**Objective**: Confirm that the `mapDBModelToAPIModel()` function handles malformed or corrupt `serving_config` JSON data in the database without crashing, returning a graceful error or omitting the field.

**Preconditions**:
- Access to the catalog database or a test fixture simulating database responses

**Test Steps**:
1. Insert or simulate a model record in the database with malformed `serving_config` JSON, for example:
   - Invalid JSON string: `'{toolCallParser: "granite"}'` (missing quotes on key)
   - Truncated JSON: `'{"toolCallParser": "gra'`
   - Null value: `NULL`
   - Wrong type: `serving_config` set to an integer instead of a JSON string
2. Call `mapDBModelToAPIModel()` with this record as input
3. Observe the function's behavior

**Expected Results**:
- The function does NOT panic or crash
- The function either returns the model without `servingConfig.toolCalling` or returns a clear error
- No unhandled exceptions propagate to the API layer
- Logging captures the malformed data for debugging purposes

**Notes**: To be filled later in the process.

---
test_case_id: TC-NEG-007
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-007: tasks array with tool-calling but no servingConfig.toolCalling detected as data inconsistency

**Objective**: Confirm that a model with `tool-calling` listed in its `tasks` array but no corresponding `servingConfig.toolCalling` block is detected as a data inconsistency — either during ingestion validation or via a data quality check.

**Preconditions**:
- Access to the model metadata ingestion pipeline or validation tooling

**Test Steps**:
1. Prepare a model metadata entry with:
   - `tasks` array containing `"tool-calling"`
   - No `servingConfig.toolCalling` block (or `servingConfig` absent entirely)
2. Run the metadata through the ingestion pipeline or data validation checks
3. Check for warnings, errors, or validation failures related to the inconsistency
4. Alternatively, query the catalog API and verify whether this inconsistency is surfaced or prevented

**Expected Results**:
- The inconsistency between `tasks` containing `tool-calling` and missing `servingConfig.toolCalling` is detected
- Either the ingestion pipeline flags or rejects the entry, OR a data quality report identifies the inconsistency
- The system does not silently publish a model that claims tool-calling support but lacks the configuration to enable it

**Notes**: To be filled later in the process.

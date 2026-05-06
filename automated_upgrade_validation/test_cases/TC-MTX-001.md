---
test_case_id: TC-MTX-001
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
---
# TC-MTX-001: New upgrade path added to matrix without pipeline changes

**Objective**: Verify that a new upgrade path can be added to the upgrade matrix declaratively, without requiring changes to the pipeline code.

**Preconditions**:
- Upgrade matrix configuration is accessible
- A new upgrade path is available for addition (e.g., 3.5→3.6)

**Test Steps**:
1. Add a new upgrade path entry to the upgrade matrix configuration (e.g., 3.5→3.6)
2. Trigger upgrade validation for a new release artifact
3. Verify the pipeline includes the newly added upgrade path

**Expected Results**:
- The new upgrade path is validated without any pipeline code changes
- The pipeline triggers validation for the new path alongside existing paths
- No pipeline errors result from the matrix configuration change

**Notes**: To be filled later in the process.

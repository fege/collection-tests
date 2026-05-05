---
test_case_id: TC-PIPE-004
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-PIPE-004: Pipeline rejects unsupported upgrade path

**Objective**: Verify that the pipeline rejects or skips an upgrade path not defined in the upgrade matrix.

**Preconditions**:
- Upgrade matrix is configured with specific supported paths
- An upgrade path NOT in the matrix is available for testing (e.g., 2.10→3.5)

**Test Steps**:
1. Attempt to trigger upgrade validation for a path not in the upgrade matrix (e.g., RHOAI 2.10→3.5)
2. Observe pipeline behavior

**Expected Results**:
- The pipeline does not execute validation for the unsupported path
- An error or skip message indicates the path is not in the supported upgrade matrix

**Notes**: To be filled later in the process.

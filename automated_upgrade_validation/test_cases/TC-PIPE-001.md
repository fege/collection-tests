---
test_case_id: TC-PIPE-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-PIPE-001: Nightly release artifact triggers upgrade validation for all matrix paths

**Objective**: Verify that a nightly release artifact automatically triggers upgrade validation for every supported upgrade path in the upgrade matrix.

**Preconditions**:
- Upgrade matrix is configured with at least two supported upgrade paths (e.g., 2.25→3.5, 3.4→3.5)
- CI pipeline infrastructure is connected to the release artifact production system

**Test Steps**:
1. Produce a nightly release artifact for RHOAI
2. Observe the CI pipeline for triggered upgrade validation jobs
3. Verify each supported upgrade path in the matrix has a corresponding validation job triggered

**Expected Results**:
- A validation job is triggered for each upgrade path defined in the upgrade matrix
- No upgrade path in the matrix is skipped
- Each triggered job references the correct nightly artifact version

**Notes**: To be filled later in the process.

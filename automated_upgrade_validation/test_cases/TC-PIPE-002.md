---
test_case_id: TC-PIPE-002
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-PIPE-002: EA release artifact triggers upgrade validation

**Objective**: Verify that an Early Access release artifact triggers upgrade validation for all supported upgrade paths.

**Preconditions**:
- Upgrade matrix is configured with supported upgrade paths
- CI pipeline infrastructure is connected to the release artifact production system

**Test Steps**:
1. Produce an EA release artifact for RHOAI
2. Observe the CI pipeline for triggered upgrade validation jobs
3. Verify each supported upgrade path has a corresponding validation job

**Expected Results**:
- A validation job is triggered for each upgrade path in the matrix
- The triggered jobs reference the correct EA artifact version
- Validation results feed into the release-readiness gate

**Notes**: To be filled later in the process.

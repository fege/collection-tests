---
test_case_id: TC-GATE-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-GATE-001: EA release blocked when upgrade validation fails

**Objective**: Verify that an EA release is blocked from promotion when upgrade validation fails for any supported upgrade path.

**Preconditions**:
- Upgrade validation pipeline is integrated with the release-readiness gate
- An upgrade validation run has completed with at least one failed upgrade path

**Test Steps**:
1. Execute upgrade validation for an EA release artifact where at least one upgrade path fails
2. Check the release-readiness gate status
3. Attempt to promote the EA release to the next stage

**Expected Results**:
- The release-readiness gate reports FAIL
- The EA release is NOT promoted to the next stage
- The gate failure message identifies which upgrade path(s) failed

**Notes**: To be filled later in the process.

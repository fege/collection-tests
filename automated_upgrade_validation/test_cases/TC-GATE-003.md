---
test_case_id: TC-GATE-003
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-GATE-003: Bodies of Water transition requires passing upgrade validation

**Objective**: Verify that upgrade-validation gates are integrated into Bodies of Water transitions so release-stage promotion requires passing upgrade stability checks.

**Preconditions**:
- Bodies of Water integration is configured
- Upgrade validation results are available for the release artifact

**Test Steps**:
1. Attempt a Bodies of Water transition for a release artifact with failing upgrade validation
2. Verify the transition is blocked
3. Retry after fixing the failing upgrade path
4. Verify the transition is now allowed

**Expected Results**:
- BoW transition is blocked when upgrade validation fails
- BoW transition is allowed when all upgrade validations pass
- The BoW gate clearly references upgrade validation status as the blocking criteria

**Notes**: To be filled later in the process.

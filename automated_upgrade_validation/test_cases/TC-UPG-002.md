---
test_case_id: TC-UPG-002
source_key: RHAISTRAT-1519
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-UPG-002: Upgrade completes within acceptable time budget

**Objective**: Verify that the platform upgrade completes within the expected time budget based on baseline measurements.

**Preconditions**:
- Cluster is running the pre-upgrade RHOAI version
- Baseline upgrade time is established (~28 minutes for 2.25.6→3.3.2)

**Test Steps**:
1. Record the start time of the platform upgrade
2. Execute the platform upgrade
3. Record the completion time
4. Compare against the baseline time budget

**Expected Results**:
- The upgrade completes within 2x the baseline time budget (e.g., under 60 minutes for a path where baseline is ~28 minutes)
- If the upgrade exceeds the time budget, a warning is raised in the pipeline output

**Notes**: To be filled later in the process.

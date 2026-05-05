---
test_case_id: TC-CLI-002
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-CLI-002: odh-cli post-upgrade action helper executes correctly

**Objective**: Verify that the odh-cli post-upgrade action helper executes required post-upgrade steps on the upgraded cluster.

**Preconditions**:
- odh-cli is installed (Golang binary)
- Platform upgrade has completed

**Test Steps**:
1. Run the odh-cli post-upgrade action command on the upgraded cluster
2. Verify each post-upgrade action completes
3. Check cluster state reflects the expected post-upgrade configuration

**Expected Results**:
- odh-cli post-upgrade command completes without error
- Each post-upgrade action is logged with its outcome
- The cluster state matches the expected post-upgrade configuration

**Notes**: To be filled later in the process.

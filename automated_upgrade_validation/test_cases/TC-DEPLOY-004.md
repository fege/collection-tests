---
test_case_id: TC-DEPLOY-004
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-DEPLOY-004: Scenario deployment failure is reported with attribution

**Objective**: Verify that a failed scenario deployment is correctly reported and attributed to the responsible component team.

**Preconditions**:
- Cluster is running the pre-upgrade RHOAI version
- A component scenario is configured to fail deployment (e.g., invalid manifest)

**Test Steps**:
1. Trigger deployment of a component scenario with an invalid or broken manifest
2. Observe the pipeline behavior on deployment failure
3. Check the published results for failure attribution

**Expected Results**:
- The pipeline detects the deployment failure
- The failure is attributed to the correct component team based on the ownership mapping
- The pipeline continues with remaining scenarios rather than aborting entirely
- The failure report includes logs and the component team identifier

**Notes**: To be filled later in the process.

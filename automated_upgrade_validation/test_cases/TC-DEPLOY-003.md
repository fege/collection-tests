---
test_case_id: TC-DEPLOY-003
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-DEPLOY-003: Deploy cross-component scenarios as pre-upgrade baseline

**Objective**: Verify that cross-component workflow scenarios are deployed as a pre-upgrade baseline on the cluster.

**Preconditions**:
- Cluster is running the pre-upgrade RHOAI version (TC-DEPLOY-001 passed)
- At least one cross-component workflow scenario manifest is available

**Test Steps**:
1. Trigger cross-component scenario deployment on the pre-upgrade cluster
2. Verify multi-component workflow resources are deployed
3. Validate the workflow executes successfully end-to-end on the pre-upgrade cluster

**Expected Results**:
- Cross-component workflow resources (deployments, services, routes) are deployed and running
- The multi-component workflow completes successfully on the pre-upgrade cluster
- Workflow output is recorded as the pre-upgrade baseline for post-upgrade comparison

**Notes**: To be filled later in the process.

---
test_case_id: TC-DEPLOY-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-DEPLOY-001: Deploy cluster at pre-upgrade RHOAI version

**Objective**: Verify that the pipeline provisions a cluster running the pre-upgrade RHOAI version as the baseline for upgrade validation.

**Preconditions**:
- Cluster provisioning automation is available
- Target pre-upgrade RHOAI version is available (e.g., RHOAI 2.25.6)

**Test Steps**:
1. Trigger upgrade validation for a supported upgrade path (e.g., 2.25.6→3.5)
2. Observe the cluster provisioning step
3. Verify the deployed cluster is running the correct pre-upgrade RHOAI version

**Expected Results**:
- A cluster is provisioned with the correct OpenShift version
- RHOAI operator is installed at the pre-upgrade version (e.g., 2.25.6)
- The RHOAI operator reports healthy status on the pre-upgrade cluster
- The cluster is ready to receive component and cross-component scenario deployments

**Notes**: To be filled later in the process.

---
test_case_id: TC-DEPLOY-002
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-DEPLOY-002: Deploy component-owned scenarios as pre-upgrade baseline

**Objective**: Verify that component-owned GitOps-based scenarios are deployed as a pre-upgrade baseline on the cluster.

**Preconditions**:
- Cluster is running the pre-upgrade RHOAI version (TC-DEPLOY-001 passed)
- At least one component team has provided a GitOps-based scenario manifest

**Test Steps**:
1. Trigger component scenario deployment on the pre-upgrade cluster
2. Verify each component's workloads are deployed
3. Validate that component endpoints and services are functional

**Expected Results**:
- Each supported component's workloads are deployed and running on the pre-upgrade cluster
- Component pods report Ready status
- Component endpoints respond to health checks
- Deployment state is recorded as the pre-upgrade baseline for post-upgrade comparison

**Notes**: To be filled later in the process.

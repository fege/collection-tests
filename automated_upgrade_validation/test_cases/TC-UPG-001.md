---
test_case_id: TC-UPG-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-UPG-001: Platform upgrade executes from pre-upgrade to target version

**Objective**: Verify that the platform upgrade from the pre-upgrade RHOAI version to the target version completes successfully.

**Preconditions**:
- Cluster is running the pre-upgrade RHOAI version (e.g., 2.25.6)
- Pre-upgrade checks and prerequisite resolution have passed
- Component and cross-component scenarios are deployed

**Test Steps**:
1. Execute the platform upgrade from pre-upgrade version to target version (e.g., 2.25.6→3.5)
2. Monitor the upgrade process for completion
3. Verify the RHOAI operator version on the upgraded cluster

**Expected Results**:
- The RHOAI operator is upgraded to the target version
- The RHOAI operator reports healthy status after upgrade
- All CRDs are updated to the target version schemas
- No pods are stuck in CrashLoopBackOff or Error state attributable to the upgrade

**Notes**: To be filled later in the process.

---
test_case_id: TC-UPG-003
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-UPG-003: Operator convergence reached after upgrade

**Objective**: Verify that all RHOAI operators reach convergence (healthy, available, not progressing) after the platform upgrade.

**Preconditions**:
- Platform upgrade has been executed (TC-UPG-001 passed)

**Test Steps**:
1. Check ClusterServiceVersion (CSV) status for the RHOAI operator
2. Verify all operator deployments report Available=True
3. Check that no operators are stuck in Progressing or Degraded state

**Expected Results**:
- RHOAI CSV phase is "Succeeded"
- All operator deployments have Available=True and Progressing=False conditions
- No operator pods are in CrashLoopBackOff or pending state

**Notes**: To be filled later in the process.

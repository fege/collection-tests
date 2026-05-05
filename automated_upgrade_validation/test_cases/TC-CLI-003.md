---
test_case_id: TC-CLI-003
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-CLI-003: Upgrade completes successfully without odh-cli dependency

**Objective**: Verify that the upgrade flow remains valid without requiring customers to depend on odh-cli — the CLI is a helper, not a gate.

**Preconditions**:
- Pre-upgrade cluster is ready for upgrade
- odh-cli is NOT installed on the cluster
- Manual upgrade documentation is available

**Test Steps**:
1. Perform pre-upgrade checks manually (without odh-cli) using documented manual steps
2. Execute the platform upgrade without odh-cli
3. Perform post-upgrade steps manually using documented manual steps
4. Validate component and cross-component scenarios

**Expected Results**:
- The upgrade completes successfully without odh-cli
- All manual steps described in the documentation are sufficient to complete the upgrade
- Component and cross-component scenarios pass post-upgrade validation

**Notes**: To be filled later in the process.

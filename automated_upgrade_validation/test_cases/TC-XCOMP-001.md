---
test_case_id: TC-XCOMP-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-XCOMP-001: Cross-component workflow survives platform upgrade

**Objective**: Verify that a realistic multi-component customer workflow deployed pre-upgrade remains functional after the platform upgrade.

**Preconditions**:
- Cross-component workflow scenarios were deployed pre-upgrade (TC-DEPLOY-003 passed)
- Platform upgrade and post-upgrade steps have completed
- Pre-upgrade workflow execution baseline is recorded

**Test Steps**:
1. Execute the cross-component validation test suite on the post-upgrade cluster
2. Trigger the same multi-component workflow that ran successfully pre-upgrade
3. Compare the workflow output against the pre-upgrade baseline

**Expected Results**:
- The cross-component workflow completes successfully post-upgrade
- Workflow output matches the pre-upgrade baseline (same results, no regressions)
- No integration failures between components that were not present pre-upgrade

**Notes**: To be filled later in the process.

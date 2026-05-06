---
test_case_id: TC-PRE-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-PRE-001: Pre-upgrade checks execute and report prerequisite status

**Objective**: Verify that pre-upgrade checks execute on the pre-upgrade cluster and report the status of all prerequisites.

**Preconditions**:
- Cluster is running the pre-upgrade RHOAI version with deployed scenarios
- odh-cli is installed and accessible

**Test Steps**:
1. Trigger the pre-upgrade checks step in the upgrade validation pipeline
2. Observe the check execution output
3. Verify each prerequisite is evaluated and its status reported

**Expected Results**:
- Pre-upgrade checks complete without crash or timeout
- Each prerequisite is listed with a pass/fail/warning status
- The aggregate prerequisite report is available in the pipeline output

**Notes**: To be filled later in the process.

---
test_case_id: TC-RPT-001
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-RPT-001: Validation results are published automatically

**Objective**: Verify that upgrade validation results are published automatically after validation completes, without manual intervention.

**Preconditions**:
- Upgrade validation pipeline has completed (pass or fail)
- Results publication system is configured

**Test Steps**:
1. Complete an upgrade validation run (successful or failed)
2. Check the results publication system for new entries
3. Verify the published results contain the validation outcome for each scenario

**Expected Results**:
- Results are published within minutes of validation completion (no manual trigger required)
- Published results include: upgrade path, validation status per scenario, pass/fail counts, execution timestamps
- Results are stored persistently (not ephemeral pipeline logs only)

**Notes**: To be filled later in the process.

---
test_case_id: TC-PRE-002
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-PRE-002: Prerequisites are resolved successfully via odh-cli

**Objective**: Verify that identified prerequisites are resolved automatically using odh-cli helpers before the upgrade proceeds.

**Preconditions**:
- Pre-upgrade checks have identified resolvable prerequisites (TC-PRE-001 executed)
- odh-cli prerequisite resolution helpers are available

**Test Steps**:
1. Execute prerequisite resolution via odh-cli on the pre-upgrade cluster
2. Re-run pre-upgrade checks to verify prerequisites are resolved
3. Verify the pipeline proceeds to the upgrade step

**Expected Results**:
- odh-cli prerequisite resolution completes without error
- Re-running pre-upgrade checks shows all prerequisites now pass
- The pipeline log records which prerequisites were resolved and how

**Notes**: To be filled later in the process.

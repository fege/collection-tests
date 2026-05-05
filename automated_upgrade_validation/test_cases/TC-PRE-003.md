---
test_case_id: TC-PRE-003
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: pre
---
# TC-PRE-003: Unresolvable prerequisite failure blocks upgrade execution

**Objective**: Verify that when pre-upgrade checks identify a critical unresolvable prerequisite, the pipeline does not proceed with the upgrade.

**Preconditions**:
- Pre-upgrade cluster has an unresolvable prerequisite (e.g., incompatible CRD schema version)
- odh-cli prerequisite resolution cannot resolve the issue

**Test Steps**:
1. Execute pre-upgrade checks on a cluster with a known unresolvable prerequisite
2. Attempt prerequisite resolution via odh-cli
3. Observe whether the pipeline proceeds to the upgrade step

**Expected Results**:
- The pipeline does NOT proceed to the platform upgrade step
- A clear error message identifies the unresolvable prerequisite
- The failure is published in the validation results with appropriate attribution

**Notes**: To be filled later in the process.

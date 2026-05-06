---
automation_status: Implemented
file: tests/test_cli_automated_upgrade_validation.py
function: test_tc_cli_001
last_updated: '2026-05-04'
priority: P1
source_key: RHAISTRAT-1519
status: Draft
test_case_id: TC-CLI-001
upgrade_phase: pre
---

# TC-CLI-001: odh-cli prerequisite resolution helper executes correctly

**Objective**: Verify that the odh-cli prerequisite resolution helper correctly identifies and resolves prerequisites on the pre-upgrade cluster.

**Preconditions**:
- odh-cli is installed (Golang binary)
- Pre-upgrade cluster has known resolvable prerequisites

**Test Steps**:
1. Run the odh-cli prerequisite check command on the pre-upgrade cluster
2. Verify it identifies known prerequisites
3. Run the odh-cli prerequisite resolution command
4. Re-run the check to verify prerequisites are resolved

**Expected Results**:
- odh-cli prerequisite check lists all known prerequisites with status
- odh-cli prerequisite resolution completes without error
- Re-running the check shows all prerequisites now pass
- Command output is structured and parseable for CI integration

**Notes**: To be filled later in the process.

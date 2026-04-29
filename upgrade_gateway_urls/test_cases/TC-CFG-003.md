---
test_case_id: TC-CFG-003
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-CFG-003: inject-auth annotation absent on unmigrated workbench

**Objective**: Confirm that workbenches created in RHOAI 2.25 that have not been migrated do not have the `inject-auth` annotation, establishing the unmigrated baseline state.

**Preconditions**:
- Code-server workbench `codeserver-wb` exists in namespace `upgrade-url-test`
- Workbench was created in RHOAI 2.25 and has not been manually annotated

**Test Steps**:
1. Query the notebook resource for the inject-auth annotation:
   ```bash
   oc get notebook codeserver-wb -n upgrade-url-test \
     -o jsonpath='{.metadata.annotations.inject-auth}'
   ```
2. Verify the output is empty (annotation not present).

**Expected Results**:
- The `oc get` command returns an empty string (no output), confirming the `inject-auth` annotation is not set
- The workbench is in the unmigrated state

**Notes**: To be filled later in the process.

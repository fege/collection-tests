---
test_case_id: TC-OP-004
source_key: RHOAIENG-48676
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-OP-004: Migration failure for one notebook does not block others

**Objective**: Verify that if migration fails for one notebook (e.g., due to a missing accelerator profile), the operator continues migrating other notebooks successfully.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Notebook `optest-notebook-fail` in namespace `optest-project` referencing a deleted accelerator profile
- Notebook `optest-notebook-pass` in namespace `optest-project` referencing a valid accelerator profile `optest-accel-valid` in `redhat-ods-applications`

**Test Steps**:
1. Trigger the hardware profile migration (operator upgrade to 3.3)
2. Wait for operator reconciliation to complete
3. Check the successful notebook for hardware profile annotations:
   ```bash
   oc get notebook optest-notebook-pass -n optest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-name}'
   oc get notebook optest-notebook-pass -n optest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```
4. Check the failed notebook for annotations (should have none):
   ```bash
   oc get notebook optest-notebook-fail -n optest-project \
     -o jsonpath='{.metadata.annotations}' | grep "hardware-profile"
   ```

**Expected Results**:
- `optest-notebook-pass` has both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations
- `optest-notebook-fail` does not have hardware profile annotations
- Operator is in Running state with no crashloop
- An event is emitted for the failed notebook

**Notes**: To be filled later in the process.

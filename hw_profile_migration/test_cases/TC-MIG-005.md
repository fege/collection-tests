---
test_case_id: TC-MIG-005
source_key: RHOAIENG-48676
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-MIG-005: Migration skips notebooks without accelerator profile references

**Objective**: Verify that notebooks without any accelerator profile reference are left untouched by the migration — no hardware profile annotations are added.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Notebook `migtest-notebook-noprofile` in namespace `migtest-project` with no accelerator profile annotation

**Test Steps**:
1. Verify the notebook has no accelerator profile annotation:
   ```bash
   oc get notebook migtest-notebook-noprofile -n migtest-project \
     -o jsonpath='{.metadata.annotations}' | grep -c "accelerator"
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Wait for operator reconciliation to complete
4. Verify no hardware profile annotations were added:
   ```bash
   oc get notebook migtest-notebook-noprofile -n migtest-project \
     -o jsonpath='{.metadata.annotations}' | grep -c "hardware-profile"
   ```

**Expected Results**:
- No `opendatahub.io/hardware-profile-name` annotation is present on the notebook
- No `opendatahub.io/hardware-profile-namespace` annotation is present on the notebook
- The notebook's existing annotations remain unchanged

**Notes**: To be filled later in the process.

---
test_case_id: TC-MIG-003
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-MIG-003: Migration creates hardware profile in global namespace from accelerator profile

**Objective**: Verify that the migration logic creates a hardware profile CR in the global namespace (`redhat-ods-applications`) when migrating from an accelerator profile.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Accelerator profile `migtest-accel-a100` created in `redhat-ods-applications` namespace
- Notebook `migtest-notebook-03` in namespace `migtest-data-science` configured to use `migtest-accel-a100`
- No hardware profile exists for this accelerator profile prior to migration

**Test Steps**:
1. Verify no hardware profile exists for this accelerator profile before migration:
   ```bash
   oc get hardwareprofile -n redhat-ods-applications | grep migtest-accel-a100
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Wait for operator reconciliation to complete
4. Check for the newly created hardware profile:
   ```bash
   oc get hardwareprofile -n redhat-ods-applications -o yaml | grep -A 5 migtest-accel-a100
   ```

**Expected Results**:
- A hardware profile CR is created in `redhat-ods-applications` namespace with a name derived from the accelerator profile (e.g., `migtest-accel-a100-notebooks`)
- The hardware profile is NOT created in the notebook's namespace (`migtest-data-science`)
- The hardware profile contains the resource configuration from the original accelerator profile

**Notes**: To be filled later in the process.

---
test_case_id: TC-MIG-001
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-MIG-001: Migration from accelerator profile adds both hardware profile annotations

**Objective**: Verify that migrating a notebook using an accelerator profile adds both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations to the notebook resource.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version that uses accelerator profiles)
- Accelerator profile `migtest-accel-gpu` created in `redhat-ods-applications` namespace
- Notebook `migtest-notebook-01` in namespace `migtest-project` configured to use `migtest-accel-gpu` accelerator profile

**Test Steps**:
1. Verify the notebook has the accelerator profile reference annotation before migration:
   ```bash
   oc get notebook migtest-notebook-01 -n migtest-project -o jsonpath='{.metadata.annotations}'
   ```
2. Trigger the hardware profile migration by upgrading the RHOAI operator to version 3.3 (containing the fix)
3. Wait for the operator reconciliation to complete (operator pod reaches Running state with no restarts)
4. Retrieve the notebook annotations after migration:
   ```bash
   oc get notebook migtest-notebook-01 -n migtest-project -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-name}'
   oc get notebook migtest-notebook-01 -n migtest-project -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```

**Expected Results**:
- Notebook has `opendatahub.io/hardware-profile-name` annotation set to the migrated hardware profile name (e.g., `migtest-accel-gpu-notebooks`)
- Notebook has `opendatahub.io/hardware-profile-namespace` annotation set to `redhat-ods-applications`
- Both annotations are present simultaneously on the notebook resource

**Notes**: To be filled later in the process.

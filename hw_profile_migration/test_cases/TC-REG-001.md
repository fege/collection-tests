---
test_case_id: TC-REG-001
source_key: RHOAIENG-48676
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-REG-001: Container-size to hardware profile migration path remains functional

**Objective**: Verify that the fix for accelerator profile migration does not break the existing container-size to hardware profile migration path — the code path at `upgrade_utils.go#L672` that was already working correctly.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Container size configuration (e.g., `Small`, `Medium`) exists
- Notebook `regtest-notebook-cs` in namespace `regtest-project` configured with a container size (not an accelerator profile)

**Test Steps**:
1. Verify the notebook is using a container size (not an accelerator profile):
   ```bash
   oc get notebook regtest-notebook-cs -n regtest-project -o jsonpath='{.metadata.annotations}'
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Wait for operator reconciliation to complete
4. Verify the notebook was migrated with both hardware profile annotations:
   ```bash
   oc get notebook regtest-notebook-cs -n regtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-name}'
   oc get notebook regtest-notebook-cs -n regtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```
5. Verify the hardware profile was created:
   ```bash
   oc get hardwareprofile -n redhat-ods-applications | grep regtest
   ```

**Expected Results**:
- Both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations are set on the notebook
- The hardware profile exists in `redhat-ods-applications`
- The container-size migration path functions identically to how it worked before the fix
- Operator remains in Running state

**Notes**: To be filled later in the process.

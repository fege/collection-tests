---
test_case_id: TC-MIG-002
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-MIG-002: Namespace annotation points to the hardware profile's actual namespace

**Objective**: Verify that the `opendatahub.io/hardware-profile-namespace` annotation on the migrated notebook correctly points to the namespace where the hardware profile was created, not the notebook's own namespace.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Accelerator profile `migtest-accel-tpu` created in `redhat-ods-applications` namespace
- Notebook `migtest-notebook-02` in namespace `migtest-userproject` configured to use `migtest-accel-tpu`

**Test Steps**:
1. Confirm the notebook is in a different namespace than the accelerator profile:
   ```bash
   oc get notebook migtest-notebook-02 -n migtest-userproject
   oc get acceleratorprofile migtest-accel-tpu -n redhat-ods-applications
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Wait for operator reconciliation to complete
4. Verify the namespace annotation value:
   ```bash
   oc get notebook migtest-notebook-02 -n migtest-userproject \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```
5. Verify the hardware profile exists in the annotated namespace:
   ```bash
   oc get hardwareprofile -n redhat-ods-applications | grep migtest-accel-tpu
   ```

**Expected Results**:
- `opendatahub.io/hardware-profile-namespace` annotation is set to `redhat-ods-applications` (the global namespace where the hardware profile was created)
- The annotation does NOT contain `migtest-userproject` (the notebook's namespace)
- The hardware profile exists in the namespace referenced by the annotation

**Notes**: To be filled later in the process.

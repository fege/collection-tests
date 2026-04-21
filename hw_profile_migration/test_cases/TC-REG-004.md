---
test_case_id: TC-REG-004
source_key: RHOAIENG-48676
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-REG-004: Mixed migration scenario with both accelerator and container-size profiles

**Objective**: Verify that when the cluster has notebooks using accelerator profiles and notebooks using container sizes, both migration paths complete successfully in the same upgrade.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Accelerator profile `regtest-accel-mixed` in `redhat-ods-applications` namespace
- Container size configuration `Medium` exists
- Notebook `regtest-notebook-accel` in `regtest-project` using `regtest-accel-mixed` accelerator profile
- Notebook `regtest-notebook-cs` in `regtest-project` using `Medium` container size

**Test Steps**:
1. Trigger the hardware profile migration (operator upgrade to 3.3)
2. Wait for operator reconciliation to complete
3. Verify accelerator-profile notebook has both annotations:
   ```bash
   oc get notebook regtest-notebook-accel -n regtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-name}'
   oc get notebook regtest-notebook-accel -n regtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```
4. Verify container-size notebook has both annotations:
   ```bash
   oc get notebook regtest-notebook-cs -n regtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-name}'
   oc get notebook regtest-notebook-cs -n regtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```
5. Verify operator status:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator
   ```

**Expected Results**:
- Both notebooks have `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations
- Both hardware profiles exist in `redhat-ods-applications`
- Operator is Running with no crashloop
- Both migration paths completed in a single upgrade cycle

**Notes**: To be filled later in the process.

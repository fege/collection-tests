---
test_case_id: TC-MIG-004
source_key: RHOAIENG-48676
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-MIG-004: Migration handles missing accelerator profile gracefully

**Objective**: Verify that if a notebook references an accelerator profile that no longer exists, the migration emits an event and does not apply hardware profile annotations, without causing an operator crashloop.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Notebook `migtest-notebook-orphan` in namespace `migtest-project` with annotation referencing accelerator profile `deleted-accel-profile`
- The referenced accelerator profile has been deleted from `redhat-ods-applications`

**Test Steps**:
1. Confirm the accelerator profile does not exist:
   ```bash
   oc get acceleratorprofile deleted-accel-profile -n redhat-ods-applications
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Wait for operator reconciliation to complete
4. Check operator logs for the migration event:
   ```bash
   oc logs -l name=rhods-operator -n redhat-ods-operator --tail=100 | grep -i "migtest-notebook-orphan"
   ```
5. Verify operator pod status:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator
   ```

**Expected Results**:
- Operator does not enter crashloop (pod status is Running, restart count stays at 0 or 1)
- An event is emitted indicating the accelerator profile was not found
- No hardware profile annotations are added to the orphaned notebook
- Other notebooks with valid accelerator profiles are still migrated successfully

**Notes**: To be filled later in the process.

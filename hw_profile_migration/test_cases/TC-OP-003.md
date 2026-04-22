---
test_case_id: TC-OP-003
source_key: RHOAIENG-48676
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-OP-003: Operator emits event when hardware profile not found during migration

**Objective**: Verify that the operator emits a Kubernetes event when it cannot find the hardware profile during migration, providing observability into migration failures without crashlooping.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Notebook `optest-notebook-event` in namespace `optest-project` referencing a deleted or non-existent accelerator profile

**Test Steps**:
1. Trigger the hardware profile migration (operator upgrade to 3.3)
2. Wait for operator reconciliation to complete
3. Check for Kubernetes events related to the notebook:
   ```bash
   oc get events -n optest-project --field-selector involvedObject.name=optest-notebook-event --sort-by='.lastTimestamp'
   ```
4. Check operator logs for the event emission:
   ```bash
   oc logs -l name=rhods-operator -n redhat-ods-operator --tail=100 | grep -i "optest-notebook-event"
   ```

**Expected Results**:
- A Kubernetes event is emitted indicating the hardware profile was not found
- The event contains the notebook name and the missing hardware profile reference
- The operator continues processing other notebooks after the event
- No crashloop occurs

**Notes**: To be filled later in the process.

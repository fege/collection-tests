---
test_case_id: TC-SM-003
source_key: RHAISTRAT-1507
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-SM-003: ServiceMonitor auto-deleted via Kubernetes GC when EvalHub CR removed

**Objective**: Verify that deleting an EvalHub CR triggers Kubernetes garbage collection to remove the associated ServiceMonitor resource.

**Preconditions**:
- EvalHub CR deployed with metrics enabled
- ServiceMonitor exists with ownerReference to the EvalHub CR (TC-SM-002 verified)

**Test Steps**:
1. Record the ServiceMonitor name for later verification
   ```bash
   SM_NAME=$(oc get servicemonitor -n opendatahub -l app=evalhub -o jsonpath='{.items[0].metadata.name}')
   ```
2. Delete the EvalHub CR
   ```bash
   oc delete evalhub evalhub-test -n opendatahub
   ```
3. Wait for Kubernetes garbage collection to process (up to 60 seconds)
4. Verify no ServiceMonitor remains for the deleted EvalHub instance
   ```bash
   oc get servicemonitor "$SM_NAME" -n opendatahub 2>&1
   ```

**Expected Results**:
- The ServiceMonitor resource is automatically removed after the EvalHub CR is deleted
- `oc get servicemonitor "$SM_NAME"` returns `NotFound`
- No orphaned ServiceMonitor resources remain in the namespace

**Notes**: To be filled later in the process.

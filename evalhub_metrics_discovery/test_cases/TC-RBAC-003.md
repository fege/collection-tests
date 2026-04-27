---
test_case_id: TC-RBAC-003
source_key: RHAISTRAT-1507
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-RBAC-003: ServiceMonitor is namespace-scoped and isolated

**Objective**: Verify that a ServiceMonitor created for an EvalHub instance in one namespace does not affect EvalHub instances or monitoring in other namespaces.

**Preconditions**:
- EvalHub deployed in `opendatahub` namespace with ServiceMonitor
- A second namespace (e.g., `test-namespace-b`) with its own EvalHub instance

**Test Steps**:
1. Deploy EvalHub in a second namespace
   ```bash
   oc apply -f evalhub-metrics-enabled.yaml -n test-namespace-b
   ```
2. Verify each namespace has its own ServiceMonitor
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   oc get servicemonitor -n test-namespace-b -l app=evalhub
   ```
3. Delete EvalHub from the second namespace
   ```bash
   oc delete evalhub evalhub-test -n test-namespace-b
   ```
4. Verify the ServiceMonitor in `opendatahub` is unaffected
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```

**Expected Results**:
- Each namespace has its own independent ServiceMonitor
- Deleting EvalHub in one namespace only removes the ServiceMonitor in that namespace
- The ServiceMonitor in the other namespace remains intact and functional

**Notes**: To be filled later in the process.

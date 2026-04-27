---
test_case_id: TC-NEG-003
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: N/A
last_updated: '2026-04-27'
automation_file: null
automation_function: null
---
# TC-NEG-003: ServiceMonitor creation fails when RBAC permissions incomplete

**Objective**: Verify that the operator fails to create a ServiceMonitor when the ClusterRole is missing the required `monitoring.coreos.com/servicemonitors` permissions, and that this failure is logged.

**Preconditions**:
- TrustyAI operator running with a modified ClusterRole that lacks ServiceMonitor permissions

**Test Steps**:
1. Remove the ServiceMonitor permissions from the operator's ClusterRole (or deploy with a pre-stripped role)
   ```bash
   oc get clusterrole <role-name> -o yaml > clusterrole-backup.yaml
   ```
2. Deploy an EvalHub CR with metrics enabled
3. Wait for operator reconciliation (up to 60 seconds)
4. Verify no ServiceMonitor is created
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```
5. Check operator logs for RBAC permission denial
   ```bash
   oc logs deployment/trustyai-service-operator -n <operator-namespace> | grep -i "forbidden\|rbac\|servicemonitor"
   ```
6. Verify the EvalHub instance remains functional (non-monitoring features work)

**Expected Results**:
- No ServiceMonitor is created due to permission denial
- Operator logs contain a `Forbidden` error for ServiceMonitor creation
- The EvalHub instance is otherwise functional
- The operator does not crash or enter a crash loop

**Notes**: To be filled later in the process.

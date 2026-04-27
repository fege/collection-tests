---
test_case_id: TC-RBAC-001
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_security.py
automation_function: null
---
# TC-RBAC-001: Operator ClusterRole has ServiceMonitor CRUD permissions

**Objective**: Verify that the TrustyAI operator's ClusterRole includes the required permissions to create, update, patch, delete, get, list, and watch ServiceMonitor resources on `monitoring.coreos.com/servicemonitors`.

**Preconditions**:
- TrustyAI Service Operator installed with ServiceMonitor support

**Test Steps**:
1. Identify the operator's ClusterRole
   ```bash
   oc get clusterrolebinding -o json | jq -r '.items[] | select(.subjects[]?.name | contains("trustyai")) | .roleRef.name'
   ```
2. Verify the ClusterRole includes ServiceMonitor permissions
   ```bash
   oc get clusterrole <role-name> -o json | jq '.rules[] | select(.resources[] == "servicemonitors")'
   ```
3. Confirm the verbs include `create`, `update`, `patch`, `delete`, `get`, `list`, `watch`
4. Confirm the API group is `monitoring.coreos.com`

**Expected Results**:
- The operator's ClusterRole has a rule for `monitoring.coreos.com` / `servicemonitors`
- All required verbs are present: `create`, `update`, `patch`, `delete`, `get`, `list`, `watch`

**Notes**: To be filled later in the process.

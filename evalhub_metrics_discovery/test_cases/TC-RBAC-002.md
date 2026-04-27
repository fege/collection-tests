---
test_case_id: TC-RBAC-002
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_security.py
automation_function: null
---
# TC-RBAC-002: Restricted user cannot modify operator-created ServiceMonitor

**Objective**: Verify that a user without ServiceMonitor management permissions cannot create, modify, or delete ServiceMonitor resources created by the operator.

**Preconditions**:
- EvalHub deployed with ServiceMonitor created
- A test user account with namespace `admin` role but no explicit ServiceMonitor permissions

**Test Steps**:
1. Impersonate a restricted user
   ```bash
   oc auth can-i delete servicemonitor -n opendatahub --as=restricted-user
   ```
2. Attempt to delete the operator-created ServiceMonitor
   ```bash
   oc delete servicemonitor <name> -n opendatahub --as=restricted-user 2>&1
   ```
3. Attempt to modify the ServiceMonitor
   ```bash
   oc patch servicemonitor <name> -n opendatahub --as=restricted-user --type merge -p '{"spec":{"endpoints":[{"interval":"60s"}]}}' 2>&1
   ```
4. Verify the ServiceMonitor remains unchanged

**Expected Results**:
- `oc auth can-i` returns `no` for delete/patch operations
- Delete and patch operations return `Forbidden` errors
- The ServiceMonitor spec is unchanged

**Notes**: To be filled later in the process.

---
test_case_id: TC-CFG-001
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-CFG-001: HTTPRoute targets correct service port for unmigrated workbench

**Objective**: Verify that the HTTPRoute resource created by the 3.x controller for an unmigrated workbench targets port 80 on the notebook Service (not port 8888), which is the fix for RHOAIENG-48747.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- Code-server workbench `codeserver-wb` is running in namespace `upgrade-url-test`
- Workbench has not been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. List HTTPRoute resources for the workbench:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=codeserver-wb -o yaml
   ```
2. Extract the backend service port from the HTTPRoute spec:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=codeserver-wb \
     -o jsonpath='{.items[0].spec.rules[0].backendRefs[0].port}'
   ```
3. Check the HTTPRoute status condition:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=codeserver-wb \
     -o jsonpath='{.items[0].status.parents[0].conditions[?(@.type=="Accepted")].status}'
   ```

**Expected Results**:
- An HTTPRoute resource exists for `codeserver-wb`
- The `backendRefs[0].port` field is `80` (not `8888`)
- The HTTPRoute status condition `Accepted` is `True`
- If the port is `8888`, the fix for RHOAIENG-48747 is not applied and traffic will fail with HTTP 500

**Notes**: To be filled later in the process.

---
test_case_id: TC-URL-003
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-URL-003: Gateway URL for unmigrated workbench does not return HTTP 500

**Objective**: Confirm that accessing an unmigrated workbench via its Gateway-based HTTPRoute URL does not produce an HTTP 500 error, validating the port mismatch fix for RHOAIENG-48747.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- Code-server workbench `codeserver-wb` is running in namespace `upgrade-url-test`
- Workbench has not been migrated (no `inject-auth: true` annotation)
- HTTPRoute resource exists for the workbench (created by the 3.x controller)

**Test Steps**:
1. Verify an HTTPRoute exists for the workbench:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=codeserver-wb
   ```
2. Extract the Gateway URL hostname from the HTTPRoute:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=codeserver-wb -o jsonpath='{.items[0].spec.hostnames[0]}'
   ```
3. Send an HTTP GET request to the Gateway URL:
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<gateway_host>/
   ```
4. Record the HTTP status code.

**Expected Results**:
- An HTTPRoute resource exists for `codeserver-wb` in namespace `upgrade-url-test`
- The `curl` command returns HTTP status code `200` (not `500`)
- If HTTP `500` is returned, the port mismatch bug (RHOAIENG-48747) has regressed: the HTTPRoute is targeting port 8888 on the Service instead of port 80

**Notes**: To be filled later in the process.

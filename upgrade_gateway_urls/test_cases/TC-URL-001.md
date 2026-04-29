---
test_case_id: TC-URL-001
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-URL-001: Legacy Route URL for Code-server workbench returns HTTP 200

**Objective**: Confirm that a Code-server workbench created in RHOAI 2.25 is accessible via its legacy OpenShift Route URL (no path component) and returns HTTP 200.

**Preconditions**:
- Code-server workbench `codeserver-wb` is running in namespace `upgrade-url-test`
- Workbench was created in RHOAI 2.25 and has not been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Retrieve the Route hostname for the Code-server workbench:
   ```bash
   oc get route codeserver-wb -n upgrade-url-test -o jsonpath='{.spec.host}'
   ```
2. Send an HTTP GET request to the Route URL, following redirects:
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<route_host>/
   ```
3. Record the HTTP status code returned.

**Expected Results**:
- The `oc get route` command returns a hostname matching the pattern `codeserver-wb-upgrade-url-test.apps.<cluster_domain>`
- The `curl` command returns HTTP status code `200`
- No HTTP `500` or `503` status code is returned

**Notes**: To be filled later in the process.

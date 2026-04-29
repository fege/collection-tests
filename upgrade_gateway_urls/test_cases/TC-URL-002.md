---
test_case_id: TC-URL-002
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-URL-002: Legacy Route URL for JupyterLab workbench (with path) returns HTTP 200

**Objective**: Confirm that a JupyterLab workbench created in RHOAI 2.25 is accessible via its legacy Route URL with the `/notebook/[namespace]/[notebook]` path component and returns HTTP 200.

**Preconditions**:
- JupyterLab workbench `jupyter-wb` is running in namespace `upgrade-url-test`
- Workbench was created in RHOAI 2.25 and has not been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Retrieve the Route hostname for the JupyterLab workbench:
   ```bash
   oc get route jupyter-wb -n upgrade-url-test -o jsonpath='{.spec.host}'
   ```
2. Send an HTTP GET request to the Route URL with the JupyterLab path, following redirects:
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<route_host>/notebook/upgrade-url-test/jupyter-wb
   ```
3. Record the HTTP status code returned.

**Expected Results**:
- The `oc get route` command returns a hostname matching the pattern `jupyter-wb-upgrade-url-test.apps.<cluster_domain>`
- The `curl` command returns HTTP status code `200`
- No HTTP `500` or `503` status code is returned

**Notes**: To be filled later in the process.

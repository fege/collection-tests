---
test_case_id: TC-UPG-002
source_key: RHOAIENG-48787
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-UPG-002: Old route does not strand users with errors after upgrade

**Objective**: Verify that accessing the old `rhods-dashboard-redhat-ods-applications` route after upgrading to RHOAI 3.3 does not result in loading errors or "Unauthorized" messages.

**Preconditions**:
- OpenShift cluster with RHOAI pre-3.3 installed
- Old dashboard route URL recorded before upgrade
- RHOAI upgrade to 3.3 completed successfully

**Test Steps**:
1. Before upgrading, record the old dashboard route URL:
   ```bash
   OLD_ROUTE=$(oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{.spec.host}')
   echo "Old route: https://${OLD_ROUTE}"
   ```
2. Complete the RHOAI upgrade to 3.3
3. Attempt to access the old route URL using curl:
   ```bash
   curl -sSL -o /dev/null -w "%{http_code}" "https://${OLD_ROUTE}"
   ```
4. Open the old route URL in a browser
5. Observe the response — check for loading errors, "Unauthorized" messages, or blank pages

**Expected Results**:
- The old route URL does NOT return a raw HTTP 401/403 "Unauthorized" error
- Users are either redirected to the new `data-science-gateway` route, shown a clear message directing them to the new location, or the old route continues to serve the dashboard
- No browser loading spinners that never resolve or cryptic error pages

**Notes**: To be filled later in the process.

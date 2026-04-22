---
test_case_id: TC-AUTH-002
source_key: RHOAIENG-48787
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-AUTH-002: No misleading "Unauthorized" error on old route after upgrade

**Objective**: Verify that authenticated users do not receive a misleading "Unauthorized" error when accessing the old `rhods-dashboard-redhat-ods-applications` route after upgrade.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Old route URL recorded before upgrade
- User authenticated with valid OpenShift credentials

**Test Steps**:
1. Authenticate with OpenShift:
   ```bash
   oc login -u <dashboard-user> -p <password> <cluster-api-url>
   ```
2. Access the old route with the authenticated token:
   ```bash
   curl -sSL -D - -H "Authorization: Bearer $(oc whoami -t)" "https://${OLD_ROUTE}"
   ```
3. Check for "Unauthorized" in the response body:
   ```bash
   curl -sSL -H "Authorization: Bearer $(oc whoami -t)" "https://${OLD_ROUTE}" | grep -i "unauthorized"
   ```
4. Open the old route URL in a browser while logged into OpenShift and observe the page content

**Expected Results**:
- Authenticated users do NOT see "Unauthorized" error messages at the old route
- The response is either a redirect to the new route, an informational page about the route change, or the dashboard itself — but never a raw 401/403 for a user with valid credentials
- The error described in RHOAIENG-48787 is resolved

**Notes**: To be filled later in the process.

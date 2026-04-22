---
test_case_id: TC-AUTH-001
source_key: RHOAIENG-48787
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-AUTH-001: Authentication works on new data-science-gateway route after upgrade

**Objective**: Verify that dashboard authentication and authorization work correctly on the new `data-science-gateway` route after upgrading to RHOAI 3.3.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- New `data-science-gateway` route is operational
- Authenticated user with dashboard access permissions available

**Test Steps**:
1. Retrieve the new dashboard route URL:
   ```bash
   NEW_ROUTE=$(oc get route -n redhat-ods-applications -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}' | grep data-science-gateway)
   echo "New route: https://${NEW_ROUTE}"
   ```
2. Access the new route without authentication and verify login redirect:
   ```bash
   curl -sSL -o /dev/null -w "%{http_code}" "https://${NEW_ROUTE}"
   ```
3. Access the new route with a valid bearer token:
   ```bash
   curl -sSL -o /dev/null -w "%{http_code}" -H "Authorization: Bearer $(oc whoami -t)" "https://${NEW_ROUTE}"
   ```
4. Open the new route in a browser, complete the OpenShift OAuth login flow, and verify dashboard access

**Expected Results**:
- Unauthenticated access triggers the standard OpenShift OAuth login flow (redirect to OAuth server)
- Authenticated access with a valid token returns HTTP 200 and renders the dashboard
- No "Unauthorized" errors for users with valid credentials
- The OAuth callback URL is correctly configured for the new route

**Notes**: To be filled later in the process.

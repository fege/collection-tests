---
test_case_id: TC-RDR-001
source_key: RHOAIENG-48787
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-RDR-001: Old route provides access continuity to new dashboard location

**Objective**: Verify that users accessing the old `rhods-dashboard-redhat-ods-applications` route after upgrade are guided to the new dashboard at `data-science-gateway` via the implemented access-continuity mechanism.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Old route URL recorded before upgrade
- New `data-science-gateway` route is operational

**Test Steps**:
1. Access the old dashboard route URL and follow any redirects:
   ```bash
   curl -sSL -v "https://${OLD_ROUTE}" 2>&1 | grep -E '< HTTP|< Location|< location'
   ```
2. If a redirect is returned, verify the Location header points to the new `data-science-gateway` route
3. If no redirect, check for an informational page or banner directing users to the new URL
4. Follow the redirect or navigate to the indicated URL and verify the dashboard loads

**Expected Results**:
- Users at the old route URL are not left stranded — they reach the dashboard via some access-continuity mechanism
- If the mechanism is an HTTP redirect (301 or 302), the Location header points to the `data-science-gateway` route
- If the mechanism is a notification/banner, it includes the new dashboard URL and clear instructions
- The final destination (new route) loads the RHOAI dashboard successfully

**Notes**: To be filled later in the process.

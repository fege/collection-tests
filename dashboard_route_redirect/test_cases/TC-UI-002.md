---
test_case_id: TC-UI-002
source_key: RHOAIENG-48787
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-UI-002: Dashboard renders correctly in browser on new route after upgrade

**Objective**: Verify that the RHOAI dashboard UI loads fully and renders correctly when accessed via the new `data-science-gateway` route in a web browser after upgrade.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- New `data-science-gateway` route is operational
- Browser (Chrome, Firefox, or Edge) available for testing

**Test Steps**:
1. Open a browser and navigate to the new dashboard route URL:
   `https://<data-science-gateway-hostname>/`
2. Complete the OpenShift OAuth login if prompted
3. Verify the dashboard home page loads:
   - Sidebar navigation is visible and functional
   - Header bar with user info is displayed
   - Main content area renders without blank sections or spinners that never resolve
4. Navigate to at least two different dashboard sections (e.g., Data Science Projects, Model Serving) to verify internal routing works
5. Check the browser developer console (F12) for JavaScript errors or failed network requests

**Expected Results**:
- Dashboard loads within a reasonable time (<10s) without loading spinners stuck indefinitely
- No "Unauthorized" or "Not Found" error pages
- All navigation elements are interactive and route to the correct pages
- No critical JavaScript errors in the browser console related to routing or authentication
- Network requests to dashboard API endpoints return successful responses

**Notes**: To be filled later in the process.

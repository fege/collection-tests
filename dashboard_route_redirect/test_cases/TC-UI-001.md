---
test_case_id: TC-UI-001
source_key: RHOAIENG-48787
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-UI-001: OpenShift console "Red Hat OpenShift AI" link navigates to new dashboard route

**Objective**: Verify that the "Red Hat OpenShift AI" link in the OpenShift console under "OpenShift Self Managed Services" navigates to the new `data-science-gateway` dashboard route after upgrade.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Authenticated user with access to the OpenShift console

**Test Steps**:
1. Log into the OpenShift web console
2. Locate the "OpenShift Self Managed Services" section in the console navigation or application launcher
3. Find and click the "Red Hat OpenShift AI" link
4. Verify the link navigates to the new `data-science-gateway` route URL
5. Verify the RHOAI dashboard loads fully without errors

**Expected Results**:
- The "Red Hat OpenShift AI" link is present and visible under "OpenShift Self Managed Services"
- Clicking the link navigates to a URL matching the `data-science-gateway` pattern
- The link does NOT point to the old `rhods-dashboard-redhat-ods-applications` pattern
- The RHOAI dashboard renders correctly after navigation

**Notes**: To be filled later in the process.

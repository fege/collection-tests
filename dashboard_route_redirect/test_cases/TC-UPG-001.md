---
test_case_id: TC-UPG-001
source_key: RHOAIENG-48787
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-UPG-001: Dashboard accessible on new route after RHOAI upgrade to 3.3

**Objective**: Verify that the RHOAI dashboard is accessible at the new `data-science-gateway` route after upgrading from pre-3.3 to 3.3.

**Preconditions**:
- OpenShift cluster running RHOAI pre-3.3 with dashboard accessible at the old `rhods-dashboard-redhat-ods-applications` route
- RHOAI 3.3 operator available via OLM catalog source
- Authenticated user with dashboard access permissions

**Test Steps**:
1. Record the current dashboard route URL before upgrade:
   ```bash
   oc get route -n redhat-ods-applications -l app=rhods-dashboard -o jsonpath='{.items[0].spec.host}'
   ```
2. Upgrade RHOAI to 3.3 via OLM by updating the operator subscription channel
3. Wait for the upgrade to complete and all pods to reach Running state:
   ```bash
   oc get pods -n redhat-ods-applications --field-selector=status.phase!=Running
   ```
4. Retrieve the new dashboard route:
   ```bash
   oc get route -n redhat-ods-applications -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.host}{"\n"}{end}' | grep -i dashboard
   ```
5. Access the new `data-science-gateway` route URL in a browser
6. Verify the dashboard loads without errors

**Expected Results**:
- The new route matching the `data-science-gateway` pattern exists after upgrade
- Accessing the new route returns HTTP 200 and the RHOAI dashboard UI renders without loading errors or "Unauthorized" messages
- All dashboard navigation elements (sidebar, header) are functional

**Notes**: To be filled later in the process.

---
test_case_id: TC-AUTH-003
source_key: RHOAIENG-48787
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-AUTH-003: RBAC permissions consistent between old and new routes

**Objective**: Verify that the same RBAC rules apply to the new `data-science-gateway` route as applied to the old route — no permission escalation or denial introduced by the route change.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Multiple test users with different roles available (dashboard-user, cluster-admin)

**Test Steps**:
1. Access the new dashboard route as a user with standard dashboard permissions:
   ```bash
   oc login -u dashboard-user -p <password> <cluster-api-url>
   curl -sSL -o /dev/null -w "%{http_code}" -H "Authorization: Bearer $(oc whoami -t)" "https://${NEW_ROUTE}"
   ```
2. Access the new dashboard route as a cluster-admin:
   ```bash
   oc login -u admin -p <password> <cluster-api-url>
   curl -sSL -o /dev/null -w "%{http_code}" -H "Authorization: Bearer $(oc whoami -t)" "https://${NEW_ROUTE}"
   ```
3. Verify that dashboard functionality visible to each role is consistent with pre-upgrade behavior
4. Check that no previously restricted actions are now available to lower-privilege users

**Expected Results**:
- Dashboard-user role can access the dashboard on the new route with the same permissions as pre-upgrade
- Cluster-admin role has the same elevated access as pre-upgrade
- No permission escalation: features restricted pre-upgrade remain restricted post-upgrade
- No permission denial: features accessible pre-upgrade remain accessible post-upgrade

**Notes**: To be filled later in the process.

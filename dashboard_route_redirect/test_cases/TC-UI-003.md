---
test_case_id: TC-UI-003
source_key: RHOAIENG-48787
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-UI-003: Console link visibility and placement under OpenShift Self Managed Services

**Objective**: Verify that the "Red Hat OpenShift AI" link is easily discoverable in the OpenShift console and placed under "OpenShift Self Managed Services" as expected.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Authenticated user with access to the OpenShift console

**Test Steps**:
1. Log into the OpenShift web console
2. Open the application launcher (grid icon in the top navigation bar)
3. Locate the "OpenShift Self Managed Services" group
4. Verify the "Red Hat OpenShift AI" entry is listed within this group
5. Check the ConsoleLink custom resource for correctness:
   ```bash
   oc get consolelinks -o yaml | grep -A 10 "openshift-ai\|rhods\|data-science"
   ```

**Expected Results**:
- The "Red Hat OpenShift AI" link appears under the "OpenShift Self Managed Services" section
- The link is visible without requiring extra scrolling or navigation
- The ConsoleLink CR exists and points to the correct `data-science-gateway` route URL

**Notes**: To be filled later in the process.

---
test_case_id: TC-UI-003
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-29'
upgrade_phase: post
---
# TC-UI-003: Newly created workbench in RHOAI 3.x shows Gateway URL and loads correctly

**Objective**: Verify that a workbench created fresh in RHOAI 3.x is assigned a Gateway-based URL by the Dashboard and that URL loads the workbench without errors. Workbenches created in 3.x automatically receive `inject-auth: true` and use the Gateway route — this is distinct from pre-upgrade workbenches which retain Route-based URLs permanently.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- A workbench named `new-wb-3x` has been created via the RHOAI 3.3 Dashboard in the `upgrade-url-test` project, and its status is `Running`
- Verify the workbench has `inject-auth: true` (set automatically by the 3.x controller on creation):
  ```bash
  oc get notebook new-wb-3x -n upgrade-url-test -o jsonpath='{.metadata.annotations.inject-auth}'
  ```

**Test Steps**:
1. Navigate to the `upgrade-url-test` project Workbenches tab in the Dashboard.
2. Locate `new-wb-3x` in the workbench list and record the URL displayed for it.
3. Click the URL link for `new-wb-3x`.
4. Verify the page that opens.

**Expected Results**:
- The URL for `new-wb-3x` uses the Gateway hostname pattern: `data-science-gateway.apps.<cluster_domain>` — not the old Route pattern (`new-wb-3x-upgrade-url-test.apps.<cluster_domain>`)
- Clicking the URL opens a browser tab that does not display an HTTP error page (no "500 Internal Server Error", no "Application is not available")

**Notes**: To be filled later in the process.

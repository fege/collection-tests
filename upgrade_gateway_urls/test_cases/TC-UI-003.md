---
test_case_id: TC-UI-003
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-UI-003: Dashboard URL switches to Gateway URL after workbench migration

**Objective**: Verify that the Dashboard URL for a workbench changes from a Route-based URL to a Gateway-based URL after the workbench is migrated by adding the `inject-auth: true` annotation.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- JupyterLab workbench `jupyter-wb` is running in namespace `upgrade-url-test`
- Workbench has not yet been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Navigate to the `upgrade-url-test` project Workbenches tab in the Dashboard.
2. Record the URL displayed for `jupyter-wb` (expected: Route-based URL).
3. Migrate the workbench by adding the annotation:
   ```bash
   oc annotate notebook jupyter-wb -n upgrade-url-test inject-auth=true
   ```
4. Wait for the controller to reconcile (up to 60 seconds).
5. Refresh the Dashboard Workbenches tab.
6. Record the URL now displayed for `jupyter-wb`.
7. Compare the pre-migration and post-migration URLs.

**Expected Results**:
- The pre-migration URL (Step 2) uses the Route hostname pattern: `jupyter-wb-upgrade-url-test.apps.<cluster_domain>`
- The post-migration URL (Step 6) uses a different hostname than the Route hostname
- The pre-migration and post-migration URLs are different strings
- Clicking the post-migration URL opens a browser tab that does not display an HTTP error page (no "500 Internal Server Error", no "Application is not available")

**Notes**: To be filled later in the process.

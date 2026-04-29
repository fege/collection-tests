---
test_case_id: TC-UI-001
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-UI-001: Dashboard displays clickable workbench URLs for unmigrated workbenches

**Objective**: Verify that the RHOAI Dashboard displays clickable URL links for unmigrated 2.x workbenches that open their respective application interfaces without HTTP errors.

**Preconditions**:
- Logged into the RHOAI Dashboard as a user with access to namespace `upgrade-url-test`
- Workbenches `jupyter-wb`, `codeserver-wb`, and `rstudio-wb` are running in namespace `upgrade-url-test`
- All workbenches are unmigrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Navigate to the Data Science Projects page in the RHOAI Dashboard.
2. Open the `upgrade-url-test` project.
3. Select the Workbenches tab and verify all three workbenches are listed with "Running" status.
4. Click the URL link displayed for `codeserver-wb` and observe the page that opens in a new tab.
5. Return to the Dashboard and click the URL link for `jupyter-wb`.
6. Return to the Dashboard and click the URL link for `rstudio-wb`.

**Expected Results**:
- Each workbench row in the Workbenches tab displays a clickable URL link
- Clicking the `codeserver-wb` URL opens a new browser tab that does not display an HTTP error page (no "500 Internal Server Error", no "Application is not available")
- Clicking the `jupyter-wb` URL opens a new browser tab that does not display an HTTP error page
- Clicking the `rstudio-wb` URL opens a new browser tab that does not display an HTTP error page
- No URL click results in a browser error page or HTTP 500 response

**Notes**: To be filled later in the process.

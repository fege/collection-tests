---
test_case_id: TC-UI-004
source_key: RHOAIENG-48747
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-UI-004: Dashboard handles stopped workbench URL display

**Objective**: Verify that the Dashboard handles URL display for stopped workbenches without rendering errors or broken UI elements.

**Preconditions**:
- Logged into the RHOAI Dashboard as a user with access to namespace `upgrade-url-test`
- Code-server workbench `codeserver-wb` exists and is currently running in namespace `upgrade-url-test`

**Test Steps**:
1. Navigate to the `upgrade-url-test` project Workbenches tab in the Dashboard.
2. Stop the `codeserver-wb` workbench using the Dashboard stop action.
3. Wait for the workbench status to change to "Stopped".
4. Observe the URL column for the stopped workbench.
5. If a URL link is still displayed, click it and observe the result.

**Expected Results**:
- The Dashboard Workbenches tab renders without JavaScript errors or blank rows for the stopped workbench
- The workbench row shows either a disabled/grayed URL link or no URL link while in "Stopped" state
- No HTTP 500 error page is displayed if a URL link is present and clicked

**Notes**: To be filled later in the process.

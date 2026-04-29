---
test_case_id: TC-UI-002
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-UI-002: Dashboard displays Route-based URL format for unmigrated workbenches

**Objective**: Verify that the Dashboard displays URLs matching the OpenShift Route hostname pattern for workbenches that have not been migrated to the new authentication model.

**Preconditions**:
- Logged into the RHOAI Dashboard as a user with access to namespace `upgrade-url-test`
- Code-server workbench `codeserver-wb` is running and has not been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Retrieve the Route hostname for the workbench:
   ```bash
   oc get route codeserver-wb -n upgrade-url-test -o jsonpath='{.spec.host}'
   ```
2. Navigate to the Data Science Projects page in the RHOAI Dashboard.
3. Open the `upgrade-url-test` project and select the Workbenches tab.
4. Inspect the URL displayed for `codeserver-wb` in the workbench row.
5. Compare the displayed URL hostname with the Route hostname retrieved in Step 1.

**Expected Results**:
- The URL displayed in the Dashboard for `codeserver-wb` contains the Route hostname from Step 1 (pattern: `codeserver-wb-upgrade-url-test.apps.<cluster_domain>`)
- The URL does not use a Gateway-based hostname that differs from the Route hostname

**Notes**: To be filled later in the process.

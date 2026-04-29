---
test_case_id: TC-UPG-001
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-UPG-001: All unmigrated workbench types accessible via legacy Routes after upgrade

**Objective**: Verify that JupyterLab, Code-server, and RStudio workbenches created in RHOAI 2.25 remain accessible via their legacy OpenShift Route URLs after upgrading to RHOAI 3.3.

**Preconditions**:
- Workbenches `jupyter-wb`, `codeserver-wb`, and `rstudio-wb` are running in namespace `upgrade-url-test`
- All workbenches were created in RHOAI 2.25 and have not been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Retrieve Route hostnames for all three workbenches:
   ```bash
   oc get route codeserver-wb -n upgrade-url-test -o jsonpath='{.spec.host}'
   oc get route jupyter-wb -n upgrade-url-test -o jsonpath='{.spec.host}'
   oc get route rstudio-wb -n upgrade-url-test -o jsonpath='{.spec.host}'
   ```
2. Access the Code-server workbench via its Route URL (no path):
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<codeserver_route_host>/
   ```
3. Access the JupyterLab workbench via its Route URL (with path):
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<jupyter_route_host>/notebook/upgrade-url-test/jupyter-wb
   ```
4. Access the RStudio workbench via its Route URL (no path):
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<rstudio_route_host>/
   ```

**Expected Results**:
- All three `oc get route` commands return a hostname (Routes exist for each workbench)
- Code-server Route URL returns HTTP `200`
- JupyterLab Route URL with `/notebook/upgrade-url-test/jupyter-wb` path returns HTTP `200`
- RStudio Route URL returns HTTP `200`
- No workbench Route URL returns HTTP `500` or `503`

**Notes**: To be filled later in the process.

---
test_case_id: TC-UPG-002
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-UPG-002: Workbench migration to inject-auth produces working Gateway URLs

**Objective**: Verify that migrating a workbench by adding the `inject-auth: true` annotation results in functional Gateway-based URLs that return HTTP 200.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- JupyterLab workbench `jupyter-wb` is running in namespace `upgrade-url-test`
- Workbench has not yet been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Verify the workbench is unmigrated:
   ```bash
   oc get notebook jupyter-wb -n upgrade-url-test \
     -o jsonpath='{.metadata.annotations.inject-auth}'
   ```
2. Migrate the workbench:
   ```bash
   oc annotate notebook jupyter-wb -n upgrade-url-test inject-auth=true
   ```
3. Wait for the controller to reconcile (up to 60 seconds).
4. Retrieve the Gateway URL from the HTTPRoute:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=jupyter-wb \
     -o jsonpath='{.items[0].spec.hostnames[0]}'
   ```
5. Access the Gateway URL:
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<gateway_host>/notebook/upgrade-url-test/jupyter-wb
   ```

**Expected Results**:
- Step 1 returns empty string (workbench is unmigrated)
- Step 2 completes without error
- Step 4 returns a Gateway hostname (not empty)
- Step 5 `curl` command returns HTTP `200`

**Notes**: To be filled later in the process.

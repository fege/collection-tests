---
test_case_id: TC-URL-004
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-URL-004: Migrated workbench accessible via Gateway URL

**Objective**: Confirm that a workbench migrated to the new authentication model (`inject-auth: true`) is accessible via its Gateway-based HTTPRoute URL with HTTP 200.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- JupyterLab workbench `jupyter-wb` in namespace `upgrade-url-test` has been migrated by adding `inject-auth: true` annotation
- HTTPRoute resource has been updated by the 3.x controller to reflect the migrated state

**Test Steps**:
1. Confirm the workbench has the `inject-auth: true` annotation:
   ```bash
   oc get notebook jupyter-wb -n upgrade-url-test -o jsonpath='{.metadata.annotations.inject-auth}'
   ```
2. Retrieve the Gateway URL hostname from the HTTPRoute:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=jupyter-wb -o jsonpath='{.items[0].spec.hostnames[0]}'
   ```
3. Send an HTTP GET request to the Gateway URL with the JupyterLab path:
   ```bash
   curl -kL -o /dev/null -s -w "%{http_code}" https://<gateway_host>/notebook/upgrade-url-test/jupyter-wb
   ```
4. Record the HTTP status code.

**Expected Results**:
- The `inject-auth` annotation value is `true`
- An HTTPRoute resource exists for the migrated workbench
- The `curl` command returns HTTP status code `200`

**Notes**: To be filled later in the process.

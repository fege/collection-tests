---
test_case_id: TC-CFG-004
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-CFG-004: inject-auth annotation present after workbench migration

**Objective**: Confirm that the `inject-auth: true` annotation is correctly set on a workbench after migration and that the 3.x controller reconciles the HTTPRoute accordingly.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- JupyterLab workbench `jupyter-wb` is running in namespace `upgrade-url-test`
- Workbench has not yet been migrated

**Test Steps**:
1. Add the inject-auth annotation to the workbench:
   ```bash
   oc annotate notebook jupyter-wb -n upgrade-url-test inject-auth=true
   ```
2. Wait for the controller to reconcile (up to 60 seconds).
3. Verify the annotation is set:
   ```bash
   oc get notebook jupyter-wb -n upgrade-url-test \
     -o jsonpath='{.metadata.annotations.inject-auth}'
   ```
4. Verify the controller has updated the HTTPRoute status:
   ```bash
   oc get httproute -n upgrade-url-test -l notebook-name=jupyter-wb \
     -o jsonpath='{.items[0].status.parents[0].conditions[?(@.type=="Accepted")].status}'
   ```

**Expected Results**:
- The `inject-auth` annotation value is `true`
- The HTTPRoute for `jupyter-wb` has status condition `Accepted` with value `True`

**Notes**: To be filled later in the process.

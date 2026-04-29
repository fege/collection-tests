---
test_case_id: TC-UPG-003
source_key: RHOAIENG-48747
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: post
---
# TC-UPG-003: Mixed-state cluster handles migrated and unmigrated workbenches

**Objective**: Verify that a post-upgrade cluster correctly routes traffic for both migrated and unmigrated workbenches simultaneously, with each using its appropriate URL type.

**Preconditions**:
- RHOAI 3.3 installed (post-upgrade from 2.25)
- Namespace `upgrade-url-test` contains:
  - `codeserver-wb`: unmigrated (no `inject-auth` annotation)
  - `jupyter-wb`: migrated (`inject-auth: true` annotation set)
- Both workbenches are running

**Test Steps**:
1. Verify migration states:
   ```bash
   oc get notebook codeserver-wb -n upgrade-url-test \
     -o jsonpath='{.metadata.annotations.inject-auth}'
   oc get notebook jupyter-wb -n upgrade-url-test \
     -o jsonpath='{.metadata.annotations.inject-auth}'
   ```
2. Access the unmigrated workbench via its legacy Route URL:
   ```bash
   CS_HOST=$(oc get route codeserver-wb -n upgrade-url-test -o jsonpath='{.spec.host}')
   curl -kL -o /dev/null -s -w "%{http_code}" https://${CS_HOST}/
   ```
3. Access the migrated workbench via its Gateway URL:
   ```bash
   JP_GW=$(oc get httproute -n upgrade-url-test -l notebook-name=jupyter-wb \
     -o jsonpath='{.items[0].spec.hostnames[0]}')
   curl -kL -o /dev/null -s -w "%{http_code}" https://${JP_GW}/notebook/upgrade-url-test/jupyter-wb
   ```

**Expected Results**:
- Step 1: `codeserver-wb` returns empty (unmigrated); `jupyter-wb` returns `true` (migrated)
- Step 2: Unmigrated `codeserver-wb` Route URL returns HTTP `200`
- Step 3: Migrated `jupyter-wb` Gateway URL returns HTTP `200`

**Notes**: To be filled later in the process.

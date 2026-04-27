---
test_case_id: TC-UPGRADE-002
source_key: RHAISTRAT-1507
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-UPGRADE-002: ClusterRole RBAC updated during operator upgrade

**Objective**: Verify that the operator's ClusterRole is correctly updated during upgrade to include `monitoring.coreos.com/servicemonitors` permissions.

**Preconditions**:
- TrustyAI operator at pre-ServiceMonitor version (ClusterRole lacks ServiceMonitor permissions)

**Test Steps**:
1. Verify the current ClusterRole does NOT include ServiceMonitor permissions
   ```bash
   oc get clusterrole <role-name> -o json | jq '.rules[] | select(.resources[]? == "servicemonitors")'
   ```
2. Upgrade the TrustyAI operator to the new version
3. Verify the ClusterRole now includes ServiceMonitor permissions
   ```bash
   oc get clusterrole <role-name> -o json | jq '.rules[] | select(.resources[]? == "servicemonitors")'
   ```
4. Verify the permissions include all required verbs (`create, update, patch, delete, get, list, watch`)

**Expected Results**:
- Before upgrade: ClusterRole has no `monitoring.coreos.com/servicemonitors` rules
- After upgrade: ClusterRole includes the full set of ServiceMonitor management permissions
- Existing EvalHub functionality is not disrupted during the upgrade

**Notes**: To be filled later in the process.

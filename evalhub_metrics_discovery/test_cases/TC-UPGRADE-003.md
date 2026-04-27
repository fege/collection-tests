---
test_case_id: TC-UPGRADE-003
source_key: RHAISTRAT-1507
priority: P2
status: Draft
automation_status: N/A
last_updated: '2026-04-27'
automation_file: null
automation_function: null
---
# TC-UPGRADE-003: No orphaned ServiceMonitor after operator rollback

**Objective**: Verify that rolling back the TrustyAI operator to a pre-ServiceMonitor version does not leave orphaned ServiceMonitor resources.

**Preconditions**:
- TrustyAI operator at ServiceMonitor-enabled version
- EvalHub instances deployed with ServiceMonitors created

**Test Steps**:
1. Record existing ServiceMonitor resources
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub -o name
   ```
2. Rollback the TrustyAI operator to the pre-ServiceMonitor version
3. Wait for reconciliation (up to 120 seconds)
4. Check if ServiceMonitor resources still exist
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```
5. If ServiceMonitors remain, verify they still have valid ownerReferences pointing to existing EvalHub CRs
6. Verify EvalHub instances continue to function (pods running, service available)

**Expected Results**:
- ServiceMonitor resources with valid `ownerReference` remain (they are owned by the EvalHub CR, not the operator)
- EvalHub instances continue to function after rollback
- No broken or dangling ServiceMonitor resources that point to non-existent targets

**Notes**: To be filled later in the process.

---
test_case_id: TC-UPGRADE-001
source_key: RHAISTRAT-1507
priority: P2
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_upgrade.py
automation_function: null
---
# TC-UPGRADE-001: Existing EvalHub instances get ServiceMonitor after operator upgrade

**Objective**: Verify that EvalHub instances deployed before the ServiceMonitor feature automatically receive a ServiceMonitor resource after upgrading the TrustyAI operator.

**Preconditions**:
- TrustyAI operator at pre-ServiceMonitor version with an existing EvalHub instance
- Metrics enabled on the existing EvalHub instance

**Test Steps**:
1. Verify no ServiceMonitor exists for the existing EvalHub instance
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```
2. Upgrade the TrustyAI operator to the version with ServiceMonitor support
3. Wait for operator reconciliation to process all existing EvalHub instances (up to 120 seconds)
4. Verify a ServiceMonitor is created for the existing EvalHub instance
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```
5. Verify the ServiceMonitor has correct spec and ownerReference

**Expected Results**:
- After operator upgrade, the existing EvalHub instance receives a ServiceMonitor
- The ServiceMonitor has correct configuration (HTTPS, `/metrics`, 30s interval)
- The ServiceMonitor has an `ownerReference` pointing to the existing EvalHub CR
- Prometheus discovers the new scrape target

**Notes**: To be filled later in the process.

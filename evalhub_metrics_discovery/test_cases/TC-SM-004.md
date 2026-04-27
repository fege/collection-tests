---
test_case_id: TC-SM-004
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-SM-004: ServiceMonitor updated when EvalHub configuration changes

**Objective**: Verify that modifying monitoring-relevant configuration in the EvalHub CR triggers the operator to update the associated ServiceMonitor resource.

**Preconditions**:
- EvalHub CR deployed with metrics enabled
- ServiceMonitor exists and is being scraped by Prometheus

**Test Steps**:
1. Record the current ServiceMonitor `resourceVersion`
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.metadata.resourceVersion}'
   ```
2. Update the EvalHub CR configuration (e.g., toggle a monitoring-relevant field)
   ```bash
   oc patch evalhub evalhub-test -n opendatahub --type merge -p '{"spec":{"prometheus":{"enabled":true}}}'
   ```
3. Wait for operator reconciliation (up to 60 seconds)
4. Verify the ServiceMonitor `resourceVersion` has changed, indicating an update
5. Verify the ServiceMonitor spec still targets the correct endpoint

**Expected Results**:
- The ServiceMonitor is updated (new `resourceVersion`) after the EvalHub CR is modified
- The updated ServiceMonitor maintains correct targeting (port `https`, path `/metrics`, interval `30s`)

**Notes**: To be filled later in the process.

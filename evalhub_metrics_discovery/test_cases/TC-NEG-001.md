---
test_case_id: TC-NEG-001
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: N/A
last_updated: '2026-04-27'
automation_file: null
automation_function: null
---
# TC-NEG-001: Operator degrades gracefully when monitoring CRD absent

**Objective**: Verify that when the `monitoring.coreos.com/v1` CRD is absent (e.g., on a dev/test cluster without Prometheus Operator), the TrustyAI operator skips ServiceMonitor creation without blocking reconciliation of the EvalHub instance.

**Preconditions**:
- OpenShift cluster without Prometheus Operator installed (or with `monitoring.coreos.com/v1` CRD removed)
- TrustyAI Service Operator running

**Test Steps**:
1. Verify the `monitoring.coreos.com/v1` CRD is not present
   ```bash
   oc get crd servicemonitors.monitoring.coreos.com 2>&1
   ```
2. Deploy an EvalHub CR with metrics enabled
   ```bash
   oc apply -f evalhub-metrics-enabled.yaml -n opendatahub
   ```
3. Wait for operator reconciliation (up to 60 seconds)
4. Verify the EvalHub instance is functional (pods running, service available)
   ```bash
   oc get pods -n opendatahub -l app=evalhub
   ```
5. Check operator logs for a warning about the missing CRD
   ```bash
   oc logs deployment/trustyai-service-operator -n <operator-namespace> | grep -i "servicemonitor\|monitoring.coreos.com"
   ```

**Expected Results**:
- EvalHub instance deploys and runs successfully despite the missing CRD
- No ServiceMonitor resource is created (as expected)
- Operator logs contain a warning or info message about the missing CRD
- The operator does NOT crash, restart, or block reconciliation of other resources

**Notes**: To be filled later in the process.

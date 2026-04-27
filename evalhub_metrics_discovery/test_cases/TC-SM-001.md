---
test_case_id: TC-SM-001
source_key: RHAISTRAT-1507
priority: P0
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_lifecycle.py
automation_function: null
---
# TC-SM-001: ServiceMonitor auto-created when EvalHub deployed with metrics enabled

**Objective**: Verify that deploying an EvalHub CR with metrics enabled (the default) triggers the TrustyAI operator to create a ServiceMonitor resource in the same namespace.

**Preconditions**:
- OpenShift cluster with RHOAI 3.5.EA1 installed
- TrustyAI Service Operator running with ServiceMonitor support
- `monitoring.coreos.com/v1` CRD available on the cluster
- No existing EvalHub instances in the test namespace

**Test Steps**:
1. Create an EvalHub CR in the `opendatahub` namespace with default configuration (metrics enabled)
   ```bash
   oc apply -f evalhub-metrics-enabled.yaml -n opendatahub
   ```
2. Wait for the operator to reconcile (up to 60 seconds)
3. Check that a ServiceMonitor resource exists in the same namespace
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```
4. Verify the ServiceMonitor targets the EvalHub Service on the HTTPS metrics port at path `/metrics`
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].path}'
   ```
5. Verify the scrape interval is 30 seconds
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].interval}'
   ```

**Expected Results**:
- A `ServiceMonitor` resource is created in the `opendatahub` namespace
- The ServiceMonitor targets port named `https` with path `/metrics`
- The scrape interval is `30s`
- The ServiceMonitor `spec.endpoints[0].scheme` is `https`

**Test Data**:
```yaml
apiVersion: trustyai.opendatahub.io/v1
kind: EvalHub
metadata:
  name: evalhub-test
  namespace: opendatahub
spec:
  prometheus:
    enabled: true
```

**Notes**: To be filled later in the process.

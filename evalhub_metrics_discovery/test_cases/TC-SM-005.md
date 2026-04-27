---
test_case_id: TC-SM-005
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-SM-005: ServiceMonitor not created when prometheus.enabled is false

**Objective**: Verify that deploying an EvalHub CR with `prometheus.enabled: false` does not create a ServiceMonitor resource.

**Preconditions**:
- OpenShift cluster with TrustyAI operator running
- No existing EvalHub instances in the test namespace

**Test Steps**:
1. Create an EvalHub CR with metrics explicitly disabled
   ```bash
   oc apply -f evalhub-metrics-disabled.yaml -n opendatahub
   ```
2. Wait for operator reconciliation (up to 60 seconds)
3. Check that no ServiceMonitor exists for this EvalHub instance
   ```bash
   oc get servicemonitor -n opendatahub -l app=evalhub
   ```

**Expected Results**:
- No `ServiceMonitor` resource is created in the namespace
- The EvalHub instance is otherwise functional (service running, pods healthy)

**Test Data**:
```yaml
apiVersion: trustyai.opendatahub.io/v1
kind: EvalHub
metadata:
  name: evalhub-no-metrics
  namespace: opendatahub
spec:
  prometheus:
    enabled: false
```

**Notes**: To be filled later in the process.

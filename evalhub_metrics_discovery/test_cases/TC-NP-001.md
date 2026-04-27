---
test_case_id: TC-NP-001
source_key: RHAISTRAT-1507
priority: P0
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_security.py
automation_function: null
---
# TC-NP-001: NetworkPolicy permits Prometheus ingress to metrics port

**Objective**: Verify that a NetworkPolicy is created that permits ingress from the Prometheus scraper in the monitoring namespace to the EvalHub metrics port.

**Preconditions**:
- EvalHub CR deployed with metrics enabled
- Namespace has NetworkPolicy enforcement enabled

**Test Steps**:
1. List NetworkPolicies in the EvalHub namespace
   ```bash
   oc get networkpolicy -n opendatahub
   ```
2. Identify the NetworkPolicy that allows ingress from the monitoring namespace to the EvalHub metrics port
   ```bash
   oc get networkpolicy -n opendatahub -o yaml | yq '.items[] | select(.spec.ingress[].from[].namespaceSelector)'
   ```
3. Verify the NetworkPolicy ingress rule allows traffic from `openshift-user-workload-monitoring` namespace
4. Verify the ingress rule targets the EvalHub metrics port

**Expected Results**:
- A NetworkPolicy exists in the EvalHub namespace that permits ingress from the Prometheus monitoring namespace
- The ingress rule targets the metrics port used by the EvalHub Service
- The NetworkPolicy `podSelector` targets EvalHub pods

**Notes**: To be filled later in the process.

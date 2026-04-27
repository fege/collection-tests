---
test_case_id: TC-NP-002
source_key: RHAISTRAT-1507
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-NP-002: Scraping succeeds in namespace with default-deny ingress

**Objective**: Verify that Prometheus can scrape the EvalHub `/metrics` endpoint in a namespace that enforces default-deny ingress policy (e.g., `opendatahub` or `redhat-ods-applications`).

**Preconditions**:
- EvalHub deployed in a namespace with default-deny NetworkPolicy
- NetworkPolicy update shipped alongside ServiceMonitor creation

**Test Steps**:
1. Verify the namespace has a default-deny ingress policy
   ```bash
   oc get networkpolicy -n opendatahub -o yaml | yq '.items[] | select(.spec.podSelector == {} and .spec.ingress == null)'
   ```
2. Deploy EvalHub CR with metrics enabled
3. Wait for ServiceMonitor and NetworkPolicy creation (up to 60 seconds)
4. Verify Prometheus can reach the `/metrics` endpoint (target state is `UP`)
   ```bash
   oc exec -n openshift-user-workload-monitoring prometheus-user-workload-0 -c prometheus -- \
     curl -s 'http://localhost:9090/api/v1/targets' | jq '.data.activeTargets[] | select(.labels.job | contains("evalhub")) | .health'
   ```

**Expected Results**:
- Despite the default-deny ingress policy, Prometheus successfully scrapes the EvalHub `/metrics` endpoint
- The Prometheus scrape target health is `up`
- The NetworkPolicy permits the specific ingress path from the monitoring namespace

**Notes**: To be filled later in the process.

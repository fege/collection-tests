---
test_case_id: TC-SCRAPE-002
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_prometheus.py
automation_function: null
---
# TC-SCRAPE-002: Metrics queryable via Prometheus query API

**Objective**: Verify that EvalHub metrics scraped by Prometheus are queryable via the Prometheus query API and visible in the OpenShift monitoring console.

**Preconditions**:
- EvalHub scrape target is UP (TC-SCRAPE-001 completed)

**Test Steps**:
1. Query Prometheus for the `up` metric filtered by the EvalHub job
   ```bash
   oc exec -n openshift-user-workload-monitoring prometheus-user-workload-0 -c prometheus -- \
     curl -s 'http://localhost:9090/api/v1/query?query=up{job=~".*evalhub.*"}'
   ```
2. Verify the query returns a result with value `1` (target is up)
3. Query for an EvalHub-specific HTTP instrumentation metric (e.g., request duration or request count)
   ```bash
   oc exec -n openshift-user-workload-monitoring prometheus-user-workload-0 -c prometheus -- \
     curl -s 'http://localhost:9090/api/v1/query?query={job=~".*evalhub.*"}' | jq '.data.result | length'
   ```
4. Verify that at least one EvalHub-specific metric is returned

**Expected Results**:
- `up{job=~".*evalhub.*"}` returns value `1`
- EvalHub HTTP instrumentation metrics are present and queryable
- Metrics include standard labels (namespace, pod, service)

**Notes**: To be filled later in the process.

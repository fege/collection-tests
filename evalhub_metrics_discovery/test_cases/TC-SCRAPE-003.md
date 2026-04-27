---
test_case_id: TC-SCRAPE-003
source_key: RHAISTRAT-1507
priority: P2
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_prometheus.py
automation_function: null
---
# TC-SCRAPE-003: Scrape interval is 30 seconds as configured

**Objective**: Verify that the Prometheus scrape interval for the EvalHub target matches the configured 30-second interval in the ServiceMonitor spec.

**Preconditions**:
- EvalHub scrape target is UP (TC-SCRAPE-001 completed)

**Test Steps**:
1. Verify the ServiceMonitor spec has a 30-second scrape interval
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].interval}'
   ```
2. Query Prometheus for the scrape interval applied to the EvalHub target
   ```bash
   oc exec -n openshift-user-workload-monitoring prometheus-user-workload-0 -c prometheus -- \
     curl -s 'http://localhost:9090/api/v1/targets' | jq '.data.activeTargets[] | select(.labels.job | contains("evalhub")) | .scrapeInterval'
   ```
3. Verify the reported scrape interval is `30s`

**Expected Results**:
- ServiceMonitor `spec.endpoints[0].interval` is `30s`
- Prometheus reports the scrape interval for EvalHub as `30s`
- Scrapes occur approximately every 30 seconds (verified via `lastScrape` timestamps)

**Notes**: To be filled later in the process.

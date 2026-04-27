---
test_case_id: TC-SCRAPE-001
source_key: RHAISTRAT-1507
priority: P0
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_prometheus.py
automation_function: null
---
# TC-SCRAPE-001: Prometheus discovers EvalHub as scrape target with state UP

**Objective**: Verify that the platform Prometheus instance discovers and scrapes the EvalHub `/metrics` endpoint, reporting the target with state `UP`.

**Preconditions**:
- EvalHub CR deployed with metrics enabled and ServiceMonitor created (TC-SM-001)
- User-workload-monitoring enabled (`enableUserWorkload: true` in `cluster-monitoring-config`)
- NetworkPolicy permits Prometheus ingress (TC-NP-001)

**Test Steps**:
1. Wait for Prometheus to discover the new scrape target (up to 90 seconds — 3x the 30s scrape interval)
2. Query the Prometheus API for the EvalHub scrape target status
   ```bash
   oc exec -n openshift-user-workload-monitoring prometheus-user-workload-0 -c prometheus -- \
     curl -s 'http://localhost:9090/api/v1/targets' | jq '.data.activeTargets[] | select(.labels.job | contains("evalhub"))'
   ```
3. Verify the target `health` field is `up`
4. Verify the `lastScrape` timestamp is recent (within 60 seconds)

**Expected Results**:
- Prometheus lists EvalHub as an active scrape target
- Target health is `up`
- The `scrapeUrl` contains the `/metrics` path over HTTPS
- `lastScrape` indicates the target was recently scraped

**Notes**: To be filled later in the process.

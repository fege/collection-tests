---
test_case_id: TC-NEG-002
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-NEG-002: Prometheus scraping blocked when NetworkPolicy missing

**Objective**: Verify that without the accompanying NetworkPolicy update, Prometheus scraping is blocked in namespaces with default-deny ingress policies, confirming both changes must ship together.

**Preconditions**:
- EvalHub deployed in a namespace with default-deny ingress
- ServiceMonitor created but NetworkPolicy for Prometheus ingress NOT applied

**Test Steps**:
1. Deploy EvalHub CR in a namespace with default-deny ingress
2. Manually create only the ServiceMonitor (without the NetworkPolicy)
3. Wait for Prometheus to attempt scraping (up to 90 seconds)
4. Check the Prometheus scrape target health
   ```bash
   oc exec -n openshift-user-workload-monitoring prometheus-user-workload-0 -c prometheus -- \
     curl -s 'http://localhost:9090/api/v1/targets' | jq '.data.activeTargets[] | select(.labels.job | contains("evalhub")) | .health'
   ```
5. Verify the target health is `down` with a connection error

**Expected Results**:
- Prometheus discovers the target via ServiceMonitor but cannot scrape it
- Target health is `down`
- The `lastError` indicates a connection timeout or refused connection
- This confirms the NetworkPolicy is essential for scraping to succeed

**Notes**: To be filled later in the process.

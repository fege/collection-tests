---
test_case_id: TC-CFG-001
source_key: RHAISTRAT-1507
priority: P1
status: Draft
automation_status: Complete
last_updated: '2026-04-27'
automation_file: tests/model_explainability/evalhub/test_evalhub_servicemonitor_lifecycle.py
automation_function: test_servicemonitor_tls_insecure_skip_verify
---
# TC-CFG-001: ServiceMonitor TLS config has insecureSkipVerify enabled

**Objective**: Verify that the ServiceMonitor's TLS configuration includes `insecureSkipVerify: true` for compatibility with OpenShift's cluster-internal certificate authority.

**Preconditions**:
- EvalHub CR deployed with metrics enabled
- ServiceMonitor created by the operator

**Test Steps**:
1. Inspect the ServiceMonitor TLS configuration
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].tlsConfig}'
   ```
2. Verify `insecureSkipVerify` is set to `true`
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].tlsConfig.insecureSkipVerify}'
   ```
3. Verify the endpoint scheme is `https`
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].scheme}'
   ```

**Expected Results**:
- `spec.endpoints[0].tlsConfig.insecureSkipVerify` is `true`
- `spec.endpoints[0].scheme` is `https`
- Prometheus can successfully scrape the endpoint using the cluster-internal CA

**Notes**: To be filled later in the process.

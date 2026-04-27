---
test_case_id: TC-CFG-002
source_key: RHAISTRAT-1507
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-CFG-002: ServiceMonitor targets correct port and path

**Objective**: Verify that the ServiceMonitor targets the EvalHub Service on the port named `https` at path `/metrics`.

**Preconditions**:
- EvalHub CR deployed with metrics enabled
- ServiceMonitor created by the operator

**Test Steps**:
1. Verify the ServiceMonitor endpoint port name
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].port}'
   ```
2. Verify the ServiceMonitor endpoint path
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.spec.endpoints[0].path}'
   ```
3. Verify the EvalHub Service has a port named `https`
   ```bash
   oc get svc -n opendatahub -l app=evalhub -o jsonpath='{.items[0].spec.ports[?(@.name=="https")].port}'
   ```

**Expected Results**:
- ServiceMonitor endpoint port is `https`
- ServiceMonitor endpoint path is `/metrics`
- The EvalHub Service exposes a port named `https` matching the ServiceMonitor target

**Notes**: To be filled later in the process.

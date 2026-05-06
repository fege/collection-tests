---
test_case_id: TC-POST-003
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-POST-003: Data-plane impact measured beyond control-plane health

**Objective**: Verify that upgrade validation measures workload continuity and data-plane impact, not only control-plane health (operator convergence).

**Preconditions**:
- Platform upgrade has completed
- Component workloads with data-plane resources (endpoints, inference routes, data pipelines) are deployed

**Test Steps**:
1. Check control-plane health (operator status, CSV phase) — this alone is NOT sufficient
2. Check data-plane health: send requests to workload endpoints deployed pre-upgrade
3. Verify service availability and response correctness from user-facing components
4. Compare data-plane metrics (response times, availability) against pre-upgrade baseline

**Expected Results**:
- Control-plane reports healthy (operators converged)
- Data-plane endpoints respond to requests with correct responses
- No workload endpoints return errors that were not present pre-upgrade
- Validation results distinguish between control-plane and data-plane health status

**Notes**: To be filled later in the process.

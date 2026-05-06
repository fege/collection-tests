---
test_case_id: TC-COMP-001
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-COMP-001: Component workloads remain functional after upgrade

**Objective**: Verify that each supported component's workloads remain functional after the platform upgrade, as validated by component-owned test suites.

**Preconditions**:
- Component-owned scenarios were deployed pre-upgrade (TC-DEPLOY-002 passed)
- Platform upgrade and post-upgrade steps have completed

**Test Steps**:
1. Execute the component-owned validation test suite for each supported component
2. Compare component workload state against the pre-upgrade baseline
3. Verify component pods, deployments, and statefulsets are in Ready state

**Expected Results**:
- Each component's validation test suite passes
- Component workloads have the same replica count as pre-upgrade (no unexpected scale-down)
- Component pods are in Running/Ready state
- No component workloads entered CrashLoopBackOff during or after upgrade

**Notes**: To be filled later in the process.

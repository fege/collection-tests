---
test_case_id: TC-POST-002
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-POST-002: Post-upgrade validation confirms workload continuity

**Objective**: Verify that post-upgrade validation checks confirm workloads deployed pre-upgrade remain functional and accessible.

**Preconditions**:
- Platform upgrade and post-upgrade steps have completed
- Component and cross-component scenarios were deployed pre-upgrade

**Test Steps**:
1. Execute post-upgrade workload continuity checks
2. Compare post-upgrade workload state against the pre-upgrade baseline
3. Verify endpoints, services, and data-plane resources remain accessible

**Expected Results**:
- Workloads that were running pre-upgrade are still running post-upgrade
- Endpoints that were responding pre-upgrade are still responding post-upgrade
- No workload pods restarted unexpectedly during the upgrade
- Data-plane impact is measured (not just operator convergence)

**Notes**: To be filled later in the process.

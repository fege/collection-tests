---
test_case_id: TC-XCOMP-002
source_key: RHAISTRAT-1519
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: post
---
# TC-XCOMP-002: Integration between components remains intact after upgrade

**Objective**: Verify that inter-component communication paths (service discovery, API calls, shared resources) remain functional after the platform upgrade.

**Preconditions**:
- Cross-component scenarios are deployed with inter-component dependencies
- Pre-upgrade integration test results are recorded

**Test Steps**:
1. Verify service discovery between components (DNS resolution, service endpoints)
2. Test API calls between components that are part of the cross-component workflow
3. Verify shared resources (ConfigMaps, Secrets, PVCs) are accessible by dependent components

**Expected Results**:
- Service discovery between components resolves correctly post-upgrade
- Inter-component API calls return the same responses as pre-upgrade
- Shared resources are accessible and contain the expected data

**Notes**: To be filled later in the process.

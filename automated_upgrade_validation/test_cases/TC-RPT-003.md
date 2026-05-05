---
test_case_id: TC-RPT-003
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-RPT-003: Failures are attributed to correct component or platform owner

**Objective**: Verify that when an upgrade validation scenario fails, the failure is attributed to the responsible component team or platform owner.

**Preconditions**:
- Component ownership mapping is configured
- An upgrade validation run includes at least one failing scenario

**Test Steps**:
1. Execute an upgrade validation run where a known component scenario fails
2. Check the published failure report for attribution
3. Verify the attributed owner matches the component ownership mapping

**Expected Results**:
- The failure report identifies the responsible component team by name
- The attribution matches the component-to-scenario ownership mapping maintained by release engineering
- The failure report distinguishes between component-level failures and platform-level failures

**Notes**: To be filled later in the process.

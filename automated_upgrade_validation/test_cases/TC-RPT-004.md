---
test_case_id: TC-RPT-004
source_key: RHAISTRAT-1519
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-RPT-004: Failure results include logs, tags, and reproduction steps

**Objective**: Verify that published failure results include sufficient context for triage: logs, component tags, and reproduction information.

**Preconditions**:
- An upgrade validation run has completed with at least one failure

**Test Steps**:
1. Review the published failure report for a known failed scenario
2. Check for the presence of logs, component tags, and reproduction context
3. Verify a triage engineer can identify the failure root cause from the report alone

**Expected Results**:
- Failure report includes relevant pod/operator logs from the failure timeframe
- Component tag identifies which team owns the failing scenario
- Report includes the upgrade path, pre-upgrade state, and the step at which failure occurred

**Notes**: To be filled later in the process.

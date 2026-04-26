---
test_case_id: TC-SDK-002
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-SDK-002: DefaultCallbacks captures per-artifact upload outcomes

**Objective**: Verify that DefaultCallbacks captures success/failure status for each artifact upload and includes per-artifact outcomes in the status event payload.

**Preconditions**:
- eval-hub-sdk v0.1.5 installed
- Fault injection proxy configured to fail specific artifact uploads

**Test Steps**:
1. Configure fault injection to fail uploads for artifacts matching path `results/metrics.json` while allowing `evaluation_report.html`
2. Run an evaluation that produces both artifacts
3. Capture the status event payload sent by DefaultCallbacks
4. Verify `artifact_uris` contains 2 entries
5. Verify `evaluation_report.html` has `status: success`
6. Verify `results/metrics.json` has `status: failed` with an `error` field describing the failure

**Expected Results**:
- Per-artifact outcomes are captured individually
- Failed artifacts include error details
- Successful artifacts are not affected by failures in other artifacts

**Notes**: To be filled later in the process.

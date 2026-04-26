---
test_case_id: TC-STATUS-001
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-STATUS-001: Partial artifact failure produces CompleteWithWarnings status

**Objective**: Verify that when evaluation succeeds but some artifact uploads fail, the job status transitions to CompleteWithWarnings.

**Preconditions**:
- Fault injection proxy configured to fail specific artifact uploads
- Evaluation job configured with MLflow tracking

**Test Steps**:
1. Configure fault injection to fail 1 of 3 artifact uploads
2. Submit an evaluation job
3. Wait for job completion
4. Query GET /api/v1/evaluations/jobs/{id}
5. Verify status is `CompleteWithWarnings`
6. Verify `artifact_uris` contains entries for all 3 artifacts
7. Verify 2 artifacts have `status: success` and 1 has `status: failed` with error details

**Expected Results**:
- Job status is `CompleteWithWarnings` (not `Complete` or `Failed`)
- Per-artifact outcomes distinguish successful from failed uploads
- Failed artifact entry includes `error` field

**Notes**: To be filled later in the process.

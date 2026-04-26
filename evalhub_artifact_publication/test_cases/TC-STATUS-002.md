---
test_case_id: TC-STATUS-002
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-STATUS-002: All artifact uploads fail produces CompleteWithWarnings (not Failed)

**Objective**: Verify that when evaluation succeeds but ALL artifact uploads fail, the job status is CompleteWithWarnings (not Failed), because the evaluation itself completed successfully.

**Preconditions**:
- Fault injection proxy configured to fail all artifact uploads
- Evaluation job configured with MLflow tracking

**Test Steps**:
1. Configure fault injection to fail all artifact uploads to MLflow
2. Submit an evaluation job
3. Wait for job completion
4. Query GET /api/v1/evaluations/jobs/{id}
5. Verify status is `CompleteWithWarnings`
6. Verify `artifact_uris` contains entries where every entry has `status: failed`
7. Verify no entry has `status: success`

**Expected Results**:
- Job status is `CompleteWithWarnings` (not `Failed`)
- All artifact entries have `status: failed` with error details
- The full-failure case is distinguishable from partial failure by examining per-artifact outcomes

**Notes**: To be filled later in the process.

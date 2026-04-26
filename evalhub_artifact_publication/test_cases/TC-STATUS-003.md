---
test_case_id: TC-STATUS-003
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-STATUS-003: Zero artifacts with MLflow tracking produces Complete with empty list

**Objective**: Verify that when MLflow tracking is enabled but the evaluation produces no artifacts, the job status is Complete and artifact_uris is an empty list.

**Test Steps**:
1. Submit an evaluation job with MLflow tracking enabled that produces zero artifacts
2. Wait for job completion
3. Query GET /api/v1/evaluations/jobs/{id}
4. Verify status is `Complete` (no warnings because there were no publication failures)
5. Verify `artifact_uris` is present and is an empty list (length 0)

**Expected Results**:
- Job status is `Complete`
- `artifact_uris` field is present with value `[]`
- No CompleteWithWarnings triggered for zero-artifact case

**Notes**: To be filled later in the process.

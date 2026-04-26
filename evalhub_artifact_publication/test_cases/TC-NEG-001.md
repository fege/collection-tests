---
test_case_id: TC-NEG-001
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-NEG-001: URI resolution timeout produces CompleteWithWarnings with uri_resolution_error

**Objective**: Verify that when the MLflow artifacts/list API call times out during URI resolution, the job status is CompleteWithWarnings with the uri_resolution_error field populated.

**Preconditions**:
- Fault injection proxy configured to introduce latency > 5s on GET /api/2.0/mlflow/artifacts/list

**Test Steps**:
1. Configure fault injection to delay MLflow artifacts/list responses beyond the 5s timeout
2. Run an evaluation job that successfully uploads artifacts
3. Wait for job completion
4. Query GET /api/v1/evaluations/jobs/{id}
5. Verify status is `CompleteWithWarnings`
6. Verify `uri_resolution_error` field is present and describes the timeout failure
7. Verify the response still includes the MLflow run_id (degraded gracefully)

**Expected Results**:
- Job status is `CompleteWithWarnings`
- `uri_resolution_error` field describes the timeout
- Artifacts may exist in MLflow but URIs could not be confirmed
- Job completion was not blocked by the timeout

**Notes**: To be filled later in the process.

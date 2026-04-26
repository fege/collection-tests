---
test_case_id: TC-NEG-002
source_key: RHAISTRAT-1525
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-NEG-002: MLflow artifacts/list network failure degrades gracefully

**Objective**: Verify that when the MLflow artifacts/list API call fails with a network error, DefaultCallbacks degrades gracefully by reporting the run ID without resolved URIs.

**Preconditions**:
- Fault injection proxy configured to return 500 errors on GET /api/2.0/mlflow/artifacts/list

**Test Steps**:
1. Configure fault injection to return HTTP 500 on MLflow artifacts/list endpoint
2. Run an evaluation job that successfully uploads artifacts
3. Capture the status event payload from DefaultCallbacks
4. Verify the payload contains `uri_resolution_error` describing the failure
5. Verify the payload still includes the MLflow run_id
6. Query GET /api/v1/evaluations/jobs/{id} and verify status is `CompleteWithWarnings`

**Expected Results**:
- DefaultCallbacks sets `uri_resolution_error` field in the status event
- Run ID is preserved for manual artifact lookup
- Job is not stuck or failed — completes with warnings

**Notes**: To be filled later in the process.

---
test_case_id: TC-SEC-002
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-SEC-002: Batch response artifact URIs use same sanitization rules as single endpoint

**Objective**: Verify that artifact URIs in batch responses are sanitized using the same rules as single-job responses — no presigned URLs, no raw S3 paths, no storage credentials.

**Preconditions**:
- EvalHub server with batch endpoint
- MLflow configured with S3-compatible storage (MinIO)
- Multiple completed jobs with artifacts

**Test Steps**:
1. Create 3 evaluation jobs with MLflow artifacts on S3-compatible storage
2. Wait for all jobs to complete
3. POST to /api/v1/evaluations/jobs/batch with all 3 job IDs
4. For each job in the batch response, for each entry in artifact_uris, validate the uri field:
   - Does NOT match pattern `s3://`
   - Does NOT match pattern `https://*.s3.`
   - Does NOT contain `X-Amz-Credential`
   - Does NOT contain `Signature=`
   - Is a run-scoped relative path
5. Compare sanitization with individual GET /api/v1/evaluations/jobs/{id} responses for the same jobs

**Expected Results**:
- All batch artifact URIs pass the same sanitization checks as single-job URIs
- No divergence between batch and single endpoint sanitization behavior

**Notes**: To be filled later in the process.

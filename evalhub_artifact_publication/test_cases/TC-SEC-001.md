---
test_case_id: TC-SEC-001
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-SEC-001: Artifact URIs must not expose raw S3/MinIO URLs or presigned URLs

**Objective**: Verify that artifact URI values in the job status response are MLflow-relative paths and do not contain raw storage URLs, presigned URLs, or storage credentials.

**Test Steps**:
1. Run an evaluation job with MLflow configured to use S3-compatible storage (MinIO)
2. Wait for successful completion
3. Query GET /api/v1/evaluations/jobs/{id}
4. For each entry in `artifact_uris`, validate that the `uri` field:
   - Does NOT match pattern `s3://`
   - Does NOT match pattern `https://*.s3.`
   - Does NOT contain `X-Amz-Credential`
   - Does NOT contain `Signature=`
   - Is a run-scoped relative path (e.g., `evaluation_report.html` or `results/metrics.json`)

**Expected Results**:
- All artifact URIs are MLflow-relative paths
- No raw S3/MinIO URLs, presigned URLs, or storage credentials are exposed
- URIs are resolvable only through the MLflow API, not directly via storage

**Notes**: To be filled later in the process.

---
test_case_id: TC-API-001
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-API-001: Job status response includes artifact URIs after successful publication

**Objective**: Verify that GET /api/v1/evaluations/jobs/{id} returns artifact URIs with resolvable MLflow-relative paths when all artifacts are published successfully.

**Preconditions**:
- EvalHub server running with eval-hub v0.3.0
- MLflow Tracking Server v3.10.1+rhaiv.1 with artifact store configured
- eval-hub-sdk v0.1.5 installed in adapter image

**Test Steps**:
1. Submit an evaluation job with MLflow tracking enabled using the lm-evaluation-harness adapter
2. Wait for the job to reach `Complete` status
3. Query GET /api/v1/evaluations/jobs/{id}
4. Verify the response contains an `artifact_uris` field
5. For each entry in `artifact_uris`, verify it contains `path`, `uri`, `content_type`, and `status` fields
6. Verify each artifact `status` is `success`
7. For each artifact, call GET /get-artifact?run_id={run_id}&path={artifact_path} to confirm the URI is resolvable

**Expected Results**:
- Response includes `artifact_uris` as a non-empty list
- Each artifact entry has `status: success`
- Each artifact path is resolvable via the MLflow Tracking API

**Test Data**:
```bash
curl -s -H "Authorization: Bearer $TOKEN" \
  "$EVALHUB_URL/api/v1/evaluations/jobs/$JOB_ID"
```

**Expected Response**:
```json
{
  "id": "job-12345",
  "status": "Complete",
  "mlflow_run_id": "abc123def456",
  "artifact_uris": [
    {
      "path": "evaluation_report.html",
      "uri": "evaluation_report.html",
      "content_type": "text/html",
      "status": "success"
    },
    {
      "path": "results/metrics.json",
      "uri": "results/metrics.json",
      "content_type": "application/json",
      "status": "success"
    }
  ]
}
```

**Validation**:
- Query PostgreSQL: `SELECT artifact_uris FROM evaluation_jobs WHERE id = 'job-12345'` — verify artifact metadata persisted

**Notes**: To be filled later in the process.

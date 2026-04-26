---
test_case_id: TC-API-002
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-API-002: Status event endpoint accepts artifact metadata in payload

**Objective**: Verify that POST /api/v1/evaluations/jobs/{id}/events correctly accepts and persists artifact metadata from the SDK status event payload.

**Preconditions**:
- EvalHub server running with extended status event schema
- Evaluation job in Running state

**Test Steps**:
1. POST a status event to /api/v1/evaluations/jobs/{id}/events with `artifact_uris` field containing 2 artifacts (1 success, 1 failed)
2. Verify the endpoint returns 200 OK
3. Query GET /api/v1/evaluations/jobs/{id} to confirm artifact metadata was persisted
4. Verify the per-artifact outcomes are correctly stored

**Expected Results**:
- POST returns 200 OK
- GET response reflects the artifact metadata from the status event
- Per-artifact status values (success/failed) are preserved

**Test Data**:
```json
{
  "type": "completion",
  "status": "CompleteWithWarnings",
  "mlflow_run_id": "run-789xyz",
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
      "status": "failed",
      "error": "Connection timeout to MLflow artifact store"
    }
  ]
}
```

**Notes**: To be filled later in the process.

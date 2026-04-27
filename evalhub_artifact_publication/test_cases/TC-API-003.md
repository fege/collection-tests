---
test_case_id: TC-API-003
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-API-003: MLflow artifacts/list API returns artifact paths via sidecar proxy

**Objective**: Verify that GET /api/2.0/mlflow/artifacts/list returns artifact paths that are resolvable through the sidecar proxy.

**Preconditions**:
- MLflow Tracking Server accessible via sidecar proxy
- At least one completed evaluation run with uploaded artifacts

**Test Steps**:
1. Identify a completed MLflow run ID from an evaluation job
2. Call GET /api/2.0/mlflow/artifacts/list?run_id={run_id} through the sidecar proxy
3. Verify the response contains a list of artifact entries
4. Verify each entry contains a `path` field with a run-scoped relative path
5. For each artifact path, verify it is retrievable via GET /get-artifact?run_id={run_id}&path={path}

**Expected Results**:
- artifacts/list returns a non-empty list of artifacts
- Artifact paths are MLflow-relative (run-scoped), not absolute storage URIs
- Artifacts are retrievable via the get-artifact endpoint

**Expected Response**:
```json
{
  "files": [
    {"path": "evaluation_report.html", "is_dir": false, "file_size": 45230},
    {"path": "results/metrics.json", "is_dir": false, "file_size": 1024}
  ]
}
```

**Notes**: To be filled later in the process.

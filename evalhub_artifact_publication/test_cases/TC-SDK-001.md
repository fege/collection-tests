---
test_case_id: TC-SDK-001
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-SDK-001: DefaultCallbacks resolves artifact URIs after upload

**Objective**: Verify that eval-hub-sdk DefaultCallbacks calls GET /api/2.0/mlflow/artifacts/list after artifact upload and includes resolved URIs in the status event payload.

**Preconditions**:
- eval-hub-sdk v0.1.5 installed
- MLflow Tracking Server accessible via sidecar proxy

**Test Steps**:
1. Configure an adapter (lm-evaluation-harness) with DefaultCallbacks and MLflow tracking enabled
2. Run an evaluation that produces at least 2 artifacts
3. Capture the status event payload sent by DefaultCallbacks to EvalHub
4. Verify the payload contains an `artifact_uris` field
5. Verify each entry has `path`, `uri`, `content_type`, and `status` fields
6. Verify the URIs were resolved by checking they match the MLflow artifacts/list response

**Expected Results**:
- DefaultCallbacks automatically resolves artifact URIs without adapter-specific code
- Status event payload includes `artifact_uris` with correct metadata
- No adapter-specific error handling was needed

**Notes**: To be filled later in the process.

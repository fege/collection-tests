---
test_case_id: TC-PERF-001
source_key: RHAISTRAT-1525
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-PERF-001: GET jobs/{id} latency increase within NFR threshold

**Objective**: Verify that adding artifact URIs to the GET /api/v1/evaluations/jobs/{id} response does not increase p99 latency by more than 50ms.

**Preconditions**:
- Baseline p99 latency measurement taken before the artifact_uris schema change
- PostgreSQL populated with evaluation jobs containing artifact metadata

**Test Steps**:
1. Record baseline p99 latency for GET /api/v1/evaluations/jobs/{id} without artifact_uris (pre-change)
2. Apply the schema migration and deploy the updated EvalHub server
3. Run 1000 requests to GET /api/v1/evaluations/jobs/{id} for jobs with artifact metadata
4. Measure p99 latency
5. Calculate the delta from baseline

**Expected Results**:
- p99 latency increase is < 50ms compared to baseline
- Artifact URIs are retrieved from local PostgreSQL, not fetched from MLflow at query time

**Notes**: To be filled later in the process.

# Test Case Index — EvalHub Artifact Publication

**Source**: [RHAISTRAT-1525](https://issues.redhat.com/browse/RHAISTRAT-1525)
**Test Plan**: [TestPlan.md](../TestPlan.md)

## Summary

| Metric | Count |
|--------|-------|
| Total Test Cases | 20 |
| P0 (Critical) | 13 |
| P1 (High) | 5 |
| P2 (Medium) | 2 |

## API Endpoint Integration Tests (TC-API)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-API-001](TC-API-001.md) | Job status response includes artifact URIs after successful publication | P0 |
| [TC-API-002](TC-API-002.md) | Status event endpoint accepts artifact metadata in payload | P0 |
| [TC-API-003](TC-API-003.md) | MLflow artifacts/list API returns artifact paths via sidecar proxy | P0 |

## eval-hub-sdk DefaultCallbacks Tests (TC-SDK)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-SDK-001](TC-SDK-001.md) | DefaultCallbacks resolves artifact URIs after upload | P0 |
| [TC-SDK-002](TC-SDK-002.md) | DefaultCallbacks captures per-artifact upload outcomes | P0 |
| [TC-SDK-003](TC-SDK-003.md) | DefaultCallbacks behavior inherited by all three adapters | P1 |

## Job Status State Transition Tests (TC-STATUS)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-STATUS-001](TC-STATUS-001.md) | Partial artifact failure produces CompleteWithWarnings status | P0 |
| [TC-STATUS-002](TC-STATUS-002.md) | All artifact uploads fail produces CompleteWithWarnings (not Failed) | P0 |
| [TC-STATUS-003](TC-STATUS-003.md) | Zero artifacts with MLflow tracking produces Complete with empty list | P0 |
| [TC-STATUS-004](TC-STATUS-004.md) | CompleteWithWarnings is a terminal state with no further transitions | P0 |

## Security Tests (TC-SEC)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-SEC-001](TC-SEC-001.md) | Artifact URIs must not expose raw S3/MinIO URLs or presigned URLs | P0 |

## Performance Tests (TC-PERF)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-PERF-001](TC-PERF-001.md) | GET jobs/{id} latency increase within NFR threshold | P2 |
| [TC-PERF-002](TC-PERF-002.md) | POST events latency increase within NFR threshold | P2 |

## Backward Compatibility and Migration Tests (TC-COMPAT)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-COMPAT-001](TC-COMPAT-001.md) | PostgreSQL schema migration succeeds without data loss | P0 |
| [TC-COMPAT-002](TC-COMPAT-002.md) | Old SDK (v0.1.2) status events accepted by new server | P1 |
| [TC-COMPAT-003](TC-COMPAT-003.md) | API schema changes are additive-only (openapi-diff) | P1 |

## Negative and Error Handling Tests (TC-NEG)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-NEG-001](TC-NEG-001.md) | URI resolution timeout produces CompleteWithWarnings with uri_resolution_error | P0 |
| [TC-NEG-002](TC-NEG-002.md) | MLflow artifacts/list network failure degrades gracefully | P1 |

## End-to-End Scenario Tests (TC-E2E)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Successful evaluation with artifact URIs surfaced in job status | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Partial artifact failure end-to-end with CompleteWithWarnings | P0 |
| [TC-E2E-003](TC-E2E-003.md) | CI/CD pipeline consumes artifact URIs for automated report retrieval | P0 |

---
feature: evalhub_artifact_publication
source_key: RHAISTRAT-1525
gap_count: 27
status: Open
last_updated: '2026-04-26'
---
# Gaps — EvalHub Artifact Publication

## Resolved Gaps (3 gaps resolved in v1.1.0)

### Environment & Infrastructure
- ✅ PostgreSQL version requirement not specified → **Resolved by**: ADR (PostgreSQL 13+ required for JSONB column features)
- ✅ Python runtime version for adapters not specified → **Resolved by**: ADR (Python 3.11+ required for eval-hub-sdk v0.1.5)

### Scope & Endpoints
- ✅ No timeout value specified for URI resolution → **Resolved by**: ADR (5s per-job timeout clarified; batch of N jobs = N×5s total budget)

## Unresolved Gaps

### Scope & Endpoints
- No explicit database schema definition for artifact_uris persistence — **Requires**: design doc or database migration spec
- No error code taxonomy for uri_resolution_error field — **Requires**: API spec or ADR
- No specification of artifact_uris JSON structure (array of objects schema) — **Requires**: OpenAPI spec or ADR
- No explicit contract for sidecar HTTP-level failure detection behavior — **Requires**: ADR or sidecar design doc

### Test Strategy & Risks
- MLflow artifact URI format specification for RHOAI downstream fork — **Requires**: API spec from MLflow/RHOAI team
- Existing monitoring/alerting query patterns for job status — **Requires**: design doc or runbook
- OpenAPI spec update details for artifact_uris field structure — **Requires**: API spec
- PostgreSQL migration rollback procedure and validation checks — **Requires**: design doc or ADR
- SDK and server version compatibility matrix — **Requires**: design doc

### Environment & Infrastructure
- OpenShift version requirement not specified — **Requires**: feature refinement or design doc
- RHOAI version requirement not specified — **Requires**: feature refinement or design doc
- Go runtime version for eval-hub components not specified — **Requires**: design doc or ADR
- Specific RBAC roles/permissions for service accounts not detailed — **Requires**: API spec or design doc
- MLflow artifact store backend type not confirmed (S3 assumed but not stated) — **Requires**: ADR or design doc
- Container registry location for adapter images not specified — **Requires**: design doc
- Specific fault injection proxy tool recommendation missing — **Requires**: ADR
- Performance testing tool recommendation missing — **Requires**: ADR
- PostgreSQL migration strategy details missing (online/offline, downtime requirements) — **Requires**: ADR
- Node count and resource limits for test cluster not specified — **Requires**: feature refinement or design doc
- Specific adapter repository URLs not provided — **Requires**: feature refinement or design doc
- Konflux build pipeline configuration details missing — **Requires**: design doc

## New Gaps Identified

### Scope & Endpoints
- Batch endpoint request/response JSON schema not fully detailed — **Requires**: OpenAPI spec
- Error response format for partial batch failures not specified (e.g., HTTP 207 vs 200 with error array) — **Requires**: API spec or ADR

### Test Strategy & Risks
- Concurrent batch request rate limiting not addressed — **Requires**: ADR specifying rate limiting policy
- Audit log retention policy undefined — **Requires**: design doc

### Environment & Infrastructure
- Audit trail schema specification missing (fields, format, storage destination) — **Requires**: ADR or design doc
- Batch endpoint pagination strategy undefined (hard cap at 50 or paginated?) — **Requires**: API spec

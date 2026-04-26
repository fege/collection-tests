---
feature: evalhub_artifact_publication
source_key: RHAISTRAT-1525
status: Open
gap_count: 24
last_updated: '2026-04-26'
---
# Gaps — EvalHub Artifact Publication

## Scope & Endpoints
- No explicit database schema definition for artifact_uris persistence — would be resolved by: ADR or database migration spec
- No timeout value specified for URI resolution — would be resolved by: ADR or configuration spec
- No error code taxonomy for uri_resolution_error field — would be resolved by: API spec or ADR
- No specification of artifact_uris JSON structure (array of objects schema) — would be resolved by: OpenAPI spec or ADR (partially described but needs formal schema)
- No explicit contract for sidecar HTTP-level failure detection behavior — would be resolved by: ADR or sidecar design doc

## Test Strategy & Risks
- MLflow artifact URI format specification for RHOAI downstream fork — would be resolved by: API spec or design doc from MLflow/RHOAI team
- Existing monitoring/alerting query patterns for job status — would be resolved by: design doc or runbook documenting current observability setup
- OpenAPI spec update details for artifact_uris field structure — would be resolved by: API spec showing full schema definition for artifact_uris list items
- PostgreSQL migration rollback procedure and validation checks — would be resolved by: design doc or ADR covering database migration strategy
- SDK and server version compatibility matrix — would be resolved by: design doc defining supported version combinations and upgrade paths

## Environment & Infrastructure
- OpenShift version requirement not specified — would be resolved by: feature refinement or design doc
- RHOAI version requirement not specified — would be resolved by: feature refinement or design doc
- PostgreSQL version requirement not specified — would be resolved by: design doc or ADR
- Python runtime version for adapters not specified — would be resolved by: design doc or ADR
- Go runtime version for eval-hub components not specified — would be resolved by: design doc or ADR
- Specific RBAC roles/permissions for service accounts not detailed — would be resolved by: API spec or design doc
- MLflow artifact store backend type not confirmed (S3 assumed but not stated) — would be resolved by: ADR or design doc
- Container registry location for adapter images not specified — would be resolved by: design doc
- Specific fault injection proxy tool recommendation missing — would be resolved by: ADR
- Performance testing tool recommendation missing — would be resolved by: ADR
- PostgreSQL migration strategy details missing (online/offline, downtime requirements) — would be resolved by: ADR
- Node count and resource limits for test cluster not specified — would be resolved by: feature refinement or design doc
- Specific adapter repository URLs not provided — would be resolved by: feature refinement or design doc
- Konflux build pipeline configuration details missing — would be resolved by: design doc

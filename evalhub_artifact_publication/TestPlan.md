---
feature: evalhub_artifact_publication
source_key: RHAISTRAT-1525
source_type: strat
version: 1.1.0
status: In Review
author: TrustyAI / Responsible AI
components:
- Model Eval
additional_docs:
- /tmp/mock-adr-evalhub.md
last_updated: '2026-04-26'
reviewers: []
---
# EvalHub Artifact Publication Test Plan
**TrustyAI / Responsible AI – EvalHub MLflow Artifact URI Surfacing and Partial Failure Signaling**

**Strategy**: [RHAISTRAT-1525](https://issues.redhat.com/browse/RHAISTRAT-1525)

---

## 1. Executive Summary

### 1.1 Purpose
This test plan validates EvalHub's enhanced MLflow artifact publication workflow, which addresses two observability gaps: (1) the job status response lacks artifact URIs after publication, requiring practitioners to manually construct paths from MLflow run IDs, and (2) partial artifact upload failures are silently absorbed with no distinguishable signal in job status. The testing effort ensures that artifact URIs are correctly surfaced in job status responses, that partial failures produce a new `CompleteWithWarnings` terminal status, and that the eval-hub-sdk provides a reusable mechanism for adapters to capture and report publication failures.

This feature is critical for downstream consumers — CI/CD pipelines, governance review dashboards, and programmatic integrations — that need to automate report retrieval without manual steps. The inability to detect partial publication failures undermines confidence in evaluation results required for compliance and audit trails.

### 1.2 Scope

#### In Scope (TrustyAI / Responsible AI Responsibilities)
- EvalHub Server API: artifact URI inclusion in GET /api/v1/evaluations/jobs/{id} response
- EvalHub Server: persistence of artifact metadata in PostgreSQL
- EvalHub Server: new `CompleteWithWarnings` terminal job status with defined state transitions
- EvalHub Server: POST /api/v1/evaluations/jobs/{id}/events endpoint extension to accept artifact metadata
- eval-hub-sdk: DefaultCallbacks implementation for URI resolution via MLflow API
- eval-hub-sdk: per-artifact upload outcome capture and status event payload construction
- Integration: all three adapters (lm-evaluation-harness, GuideLLM, LightEval) inherit DefaultCallbacks behavior
- Error handling: partial failure detection, URI resolution timeouts, per-artifact outcome reporting
- Schema validation: additive-only API changes verified by openapi-diff
- Security: artifact URI format validation (must not expose raw S3/MinIO URLs or presigned URLs)
- EvalHub Server: batch job status retrieval endpoint (POST /api/v1/evaluations/jobs/batch)
- Audit: artifact URI resolution logging to audit trail
- Security: URI sanitization rules applied to batch responses

#### Out of Scope (Other Teams)
- Changes to EvalHub report content or format
- Artifact lifecycle management in MLflow (retention, access controls)
- EvalHub UI changes for report display
- Artifact publication behavior itself (already implemented and default)
- Signed artifact attestation (RHAIRFE-1751 OCI EvalCard signing)
- Artifact re-upload or retry from `CompleteWithWarnings` state
- Adding application-level processing logic to the sidecar
- Changes to MLflow server code or artifact storage backend
- Changes to the EvalHub CR or CRD schema (trustyai-service-operator)

### 1.3 Test Objectives
1. Verify that GET /api/v1/evaluations/jobs/{id} returns artifact URIs with resolvable MLflow-relative paths after successful job completion with MLflow tracking enabled
2. Validate that partial artifact publication failures produce a `CompleteWithWarnings` job status with per-artifact failure details
3. Confirm that complete artifact publication failures (all artifacts fail) result in `CompleteWithWarnings` status while evaluation itself succeeds
4. Verify that URI resolution failures or timeouts produce `CompleteWithWarnings` status with `uri_resolution_error` field populated
5. Validate that jobs with zero artifacts complete with `Complete` status and empty `artifact_uris` list
6. Confirm that all three adapters (lm-evaluation-harness, GuideLLM, LightEval) inherit URI resolution and failure reporting behavior via DefaultCallbacks without adapter-specific code
7. Verify that API schema changes are additive-only and backward-compatible via openapi-diff validation
8. Verify that POST /api/v1/evaluations/jobs/batch correctly accepts a list of job IDs and returns an array of job status objects with artifact_uris
9. Validate that all artifact URI resolutions are logged to the audit trail with timestamp, job_id, run_id, and success/failure status
10. Confirm that the per-job timeout (5s) applies independently to each job in a batch request, not to the entire batch operation

---

## 2. Test Strategy

### 2.1 Test Levels
- **API Integration Testing** — REST endpoint testing for GET /api/v1/evaluations/jobs/{id} and POST /api/v1/evaluations/jobs/{id}/events extensions; MLflow artifact listing and retrieval APIs
- **Data Validation Testing** — PostgreSQL schema migration validation, artifact_uris field structure validation, per-artifact outcome capture, status event schema validation
- **Functional Testing** — CompleteWithWarnings status logic, DefaultCallbacks URI resolution, partial publication failure detection, artifact URI format validation (MLflow-relative path requirements)
- **Security Testing** — Artifact URI sanitization (must reject s3://, https://*.s3., X-Amz-Credential, Signature= patterns), sidecar proxy authentication, presigned URL prevention
- **Performance Testing** — GET jobs/{id} p99 latency < 50ms increase, POST events p99 latency < 100ms increase, MLflow API call impact on job completion timing

### 2.2 Test Types
- **Positive Testing** — Complete job with all artifacts published successfully, artifact_uris returned with valid MLflow paths, CompleteWithWarnings with partial failure metadata
- **Negative Testing** — MLflow artifact listing failures, URI resolution errors captured in status events, invalid artifact URI format rejection, network failures during artifact publication
- **Boundary Testing** — Large artifact counts, concurrent job completions, PostgreSQL migration rollback scenarios, SDK version compatibility matrix
- **Regression Testing** — Existing adapters with SDK v0.1.2 continue to function, existing monitoring/alerting tolerates CompleteWithWarnings, Complete and Failed statuses unchanged

### 2.3 Test Priorities
- **P0 (Critical)** — Artifact URI(s) returned in job status response; partial publication failures produce CompleteWithWarnings status; security: artifact URIs must not expose raw S3/MinIO URLs or presigned URLs
- **P1 (High)** — DefaultCallbacks resolve URIs and capture per-artifact outcomes; status event schema extensions correctly persist and retrieve; backwards compatibility with older SDK versions
- **P2 (Medium)** — Artifact URI format stability across MLflow releases; OpenAPI spec accuracy; performance NFRs met

---

## 3. Test Environment

### 3.1 Test Cluster Configuration
- OpenShift cluster (version TBD)
- RHOAI deployment (version TBD, with MLflow v3.10.1+rhaiv.1)
- PostgreSQL 13+ database for EvalHub server
- Python 3.11+ runtime for adapter containers (lm-evaluation-harness, GuideLLM, LightEval)
- Go runtime for eval-hub server and sidecar components
- MLflow Tracking Server v3.10.1+rhaiv.1 with artifact store backend
- trustyai-service-operator deployed

### 3.2 Test Data Requirements
- Sample MLflow run IDs for artifact retrieval testing
- Test evaluation job configurations with artifact publication scenarios
- PostgreSQL seed data for existing jobs (backward compatibility testing)
- Mock evaluation results and artifacts for upload testing (various sizes and types: metrics, plots, models)
- Provider YAML configurations from config/providers/*.yaml
- Schema migration scripts for PostgreSQL artifact_uris column
- Sample API request/response payloads for job status, events, and MLflow API responses
- Sample evaluation job dataset with 50+ completed jobs containing artifact metadata
- Batch endpoint test payloads with up to 50 job IDs

### 3.3 Test Users
- Service account with permissions to create/read evaluation jobs
- Service account with MLflow Tracking API access (read/write artifacts)
- Service account with PostgreSQL database access for migration testing
- Admin user for operator installation and configuration

---

## 4. API Endpoints Under Test

| Endpoint | Method | Purpose | Priority |
|----------|--------|---------|----------|
| /api/v1/evaluations/jobs/{id} | GET | Retrieve job status including artifact URIs and per-artifact outcomes | P0 |
| /api/v1/evaluations/jobs/{id}/events | POST | Accept status events with artifact metadata from SDK | P0 |
| /api/2.0/mlflow/artifacts/list?run_id={run_id} | GET | Resolve artifact URIs after upload (MLflow API via sidecar) | P0 |
| /get-artifact?run_id={run_id}&path={artifact_path} | GET | Retrieve artifacts from MLflow for validation | P1 |
| eval-hub-sdk DefaultCallbacks (artifact URI resolution) | Python Method | Capture artifact URIs and publication failures in DefaultCallbacks | P0 |
| eval-hub-sdk DefaultCallbacks (status event payload) | Python Method | Construct artifact_uris and uri_resolution_error fields | P0 |
| EvalHub PostgreSQL schema migration | Database | Persist artifact metadata and CompleteWithWarnings status | P0 |
| openapi-diff validation | CLI | Verify schema changes are additive-only | P1 |
| /api/v1/evaluations/jobs/batch | POST | Retrieve multiple job statuses with artifact_uris in a single request | P0 |

---

## 5. Test Cases

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-API | 4 | 4 P0 |
| TC-SDK | 3 | 2 P0, 1 P1 |
| TC-STATUS | 4 | 4 P0 |
| TC-SEC | 2 | 2 P0 |
| TC-PERF | 3 | 1 P1, 2 P2 |
| TC-COMPAT | 3 | 1 P0, 2 P1 |
| TC-NEG | 3 | 2 P0, 1 P1 |
| TC-E2E | 4 | 4 P0 |
| **Total** | **26** | **17 P0, 6 P1, 3 P2** |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- `TC-API` — API endpoint integration tests (job status, events)
- `TC-SDK` — eval-hub-sdk DefaultCallbacks behavior tests
- `TC-STATUS` — Job status state transition tests (Complete, CompleteWithWarnings, Failed)
- `TC-SEC` — Security tests (URI format validation, credential leak prevention)
- `TC-PERF` — Performance tests (latency, throughput)
- `TC-COMPAT` — Backward compatibility and migration tests
- `TC-E2E` — End-to-end scenario tests
- `TC-NEG` — Negative and error handling tests

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Successful evaluation with artifact URIs surfaced in job status | GET jobs/{id}, POST events, GET artifacts/list, GET get-artifact, DefaultCallbacks (both) | P0 |
| TC-E2E-002 | Partial artifact failure end-to-end with CompleteWithWarnings | GET jobs/{id}, POST events, DefaultCallbacks (both), PostgreSQL | P0 |
| TC-E2E-003 | CI/CD pipeline consumes artifact URIs for automated report retrieval | GET jobs/{id}, GET get-artifact | P0 |
| TC-E2E-004 | Batch status retrieval for governance dashboard with mixed job outcomes | POST jobs/batch, GET get-artifact | P0 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| GET /api/v1/evaluations/jobs/{id} | TC-E2E-001, TC-E2E-002, TC-E2E-003 |
| POST /api/v1/evaluations/jobs/{id}/events | TC-E2E-001, TC-E2E-002 |
| GET /api/2.0/mlflow/artifacts/list | TC-E2E-001 |
| GET /get-artifact | TC-E2E-001, TC-E2E-003, TC-E2E-004 |
| DefaultCallbacks (URI resolution) | TC-E2E-001, TC-E2E-002 |
| DefaultCallbacks (status event payload) | TC-E2E-001, TC-E2E-002 |
| PostgreSQL schema migration | TC-E2E-002 |
| openapi-diff validation | — (covered by TC-COMPAT-003) |
| POST /api/v1/evaluations/jobs/batch | TC-E2E-004 |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

**Not Applicable** — The feature extends artifact metadata retrieval from an existing MLflow deployment. It does not introduce new external registry dependencies, image pulls, or catalog sources. Testing focuses on API changes and data persistence within an existing cluster.

### 7.2 Upgrade/Migration

- **PostgreSQL Schema Migration** — Test migration from current schema to extended schema with artifact_uris field; validate rollback scenarios; ensure no data loss during upgrade
- **SDK Version Compatibility** — Test server with eval-hub-sdk v0.1.2 (older) and v0.1.5 (new) during rolling upgrades; verify artifact_uris field omitted or empty when SDK does not provide it
- **Status Enum Extension** — Verify existing monitoring/alerting systems tolerate CompleteWithWarnings; test backwards compatibility with clients expecting only Complete/Failed
- **Adapter Rebuilds** — Test provider YAML digest updates trigger adapter image pulls correctly; verify lm-evaluation-harness, GuideLLM, LightEval adapters function with new SDK

### 7.3 Performance/Scalability

- **API Latency** — Measure p99 latency increase for GET /api/v1/evaluations/jobs/{id} (target: < 50ms increase) and POST /api/v1/evaluations/jobs/{id}/events (target: < 100ms increase)
- **MLflow API Impact** — Measure impact of GET /api/2.0/mlflow/artifacts/list on job completion timing; test under concurrent job completions
- **Resource Consumption** — Validate PostgreSQL storage impact of artifact_uris field for large artifact counts; test query performance with new schema
- **Batch Endpoint Latency** — p99 latency must remain under 200ms for batch sizes up to 50 job IDs
- **Timeout Budget** — per-job timeout of 5s is correctly enforced (batch of 10 jobs = 50s total budget, not 5s total)

### 7.4 RBAC/Authorization

- **Batch Response Authorization** — Verify batch endpoint applies same RBAC filtering as single job endpoint (users only see jobs they have access to)
- **Cross-Tenant Isolation** — Batch request cannot retrieve job IDs from another tenant's namespace

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| MLflow artifact URI format varies by storage backend (S3, MinIO, Azure Blob) and upstream/downstream fork | High | High | Test against RHOAI downstream MLflow fork and supported storage backends; implement URI format validation regex; document expected formats; add integration tests with MinIO and S3-compatible storage |
| CompleteWithWarnings status confuses existing monitoring/alerting systems that expect only Complete/Failed | Medium | Medium | Survey existing monitoring queries before deployment; provide migration guide; ensure CompleteWithWarnings is additive; document in release notes |
| SDK v0.1.2 and v0.1.5 version skew during rolling upgrades causes malformed artifact metadata | Medium | High | Test all version combinations (server + SDK matrix); ensure server tolerates missing artifact_uris field; validate backwards compatibility; document upgrade order |
| MLflow artifacts/list API behavior differs in RHOAI downstream fork vs upstream MLflow | High | Medium | Test against RHOAI downstream MLflow deployment; compare response schema with upstream; document differences; implement adapter layer if formats diverge |
| URI resolution adds MLflow API call latency, potentially blocking job completion | Medium | Medium | Implement 5s timeout for MLflow artifacts/list call; measure p99 latency impact; degrade gracefully by setting uri_resolution_error without blocking |
| PostgreSQL schema migration failure or rollback during production deployment | High | Low | Test migration in staging with production-like data volume; implement automated rollback; validate migration idempotency |
| Adapter rebuilds with provider YAML digest updates fail to pull new images or reference wrong SDK version | Medium | Low | Test provider YAML digest update workflow in staging; validate all three adapter images contain SDK v0.1.5; add smoke tests |
| Batch timeout budget misinterpretation (5s per-job vs per-batch) causes client-side timeouts | High | Medium | Add integration tests for timeout budget calculation with varying batch sizes |
| Audit logging volume at scale degrades database write performance | High | Medium | Implement audit log batching/buffering, test throughput with concurrent batch requests |
| Partial batch failures (some job IDs valid, some invalid) lack clear error semantics | Medium | High | Define batch response error format, add tests for mixed valid/invalid job IDs |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure
- Single OpenShift cluster with RHOAI deployment
- PostgreSQL database server accessible from eval-hub server pods
- MLflow Tracking Server with artifact store backend (S3-compatible storage)
- Konflux build pipelines for rebuilding adapter images
- Container registry for storing adapter images with updated eval-hub-sdk versions
- HTTP proxy infrastructure for fault injection testing (selectively failing PUT requests to MLflow)
- Audit trail infrastructure for artifact URI resolution logging

### 9.2 Configuration
- eval-hub server environment variables (database connection string, MLflow tracking URI)
- eval-hub-sdk configuration in adapter images (DefaultCallbacks enabled, MLflow tracking URI)
- Provider YAML configs (config/providers/*.yaml) with updated image digests for lm-evaluation-harness, GuideLLM, and LightEval adapters
- Sidecar proxy configuration for credential injection
- PostgreSQL migration scripts for artifact_uris schema change
- MLflow artifact store configuration (S3 bucket, credentials)

### 9.3 Test Tools
- API testing: curl, httpie, or similar for REST API validation
- Database tools: psql for PostgreSQL query and migration verification
- Kubernetes tools: kubectl/oc for pod inspection, logs, and resource management
- HTTP proxy tools: mitmproxy, toxiproxy, or similar for fault injection testing
- Performance testing: wrk, hey, or similar for p99 latency verification
- MLflow CLI: mlflow (Python CLI) for artifact inspection and verification
- Build tools: Konflux CLI/API for triggering adapter image rebuilds
- Schema validation: openapi-diff for additive-only schema change verification

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-API | 4 | 4 | 0 | 0 |
| TC-SDK | 3 | 2 | 1 | 0 |
| TC-STATUS | 4 | 4 | 0 | 0 |
| TC-SEC | 2 | 2 | 0 | 0 |
| TC-PERF | 3 | 0 | 1 | 2 |
| TC-COMPAT | 3 | 1 | 2 | 0 |
| TC-NEG | 3 | 2 | 1 | 0 |
| TC-E2E | 4 | 4 | 0 | 0 |
| **Total** | **26** | **17** | **6** | **3** |

### 10.2 Endpoint Coverage

| Endpoint | Test Cases | Coverage |
|----------|------------|----------|
| GET /api/v1/evaluations/jobs/{id} | TC-API-001, TC-STATUS-001, TC-STATUS-002, TC-STATUS-003, TC-STATUS-004, TC-E2E-001, TC-E2E-002, TC-E2E-003 | |
| POST /api/v1/evaluations/jobs/{id}/events | TC-API-002, TC-E2E-001, TC-E2E-002 | |
| GET /api/2.0/mlflow/artifacts/list | TC-API-003, TC-E2E-001 | |
| GET /get-artifact | TC-API-001, TC-E2E-001, TC-E2E-003 | |
| DefaultCallbacks (URI resolution) | TC-SDK-001, TC-SDK-003, TC-NEG-001, TC-NEG-002, TC-E2E-001, TC-E2E-002 | |
| DefaultCallbacks (status event payload) | TC-SDK-002, TC-SDK-003, TC-E2E-001, TC-E2E-002 | |
| PostgreSQL schema migration | TC-COMPAT-001, TC-E2E-002 | |
| openapi-diff validation | TC-COMPAT-003 | |
| POST /api/v1/evaluations/jobs/batch | TC-API-004, TC-SEC-002, TC-PERF-003, TC-NEG-003, TC-E2E-004 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-26 | Initial test plan |
| 1.1.0 | 2026-04-26 | Updated with ADR: batch endpoint, audit logging, PostgreSQL 13+, Python 3.11+; resolved 3 gaps; added 3 risks |

---

**End of Test Plan**

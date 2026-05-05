---
feature: automated_upgrade_validation
source_key: RHAISTRAT-1519
status: Open
gap_count: 33
last_updated: '2026-05-04'
---
# Gaps — Automated Upgrade Validation

> **Note**: The Feature Refinement Document (RHAISTRAT-1519) resolved 4 gaps and clarified requirements, but added 3 new mandatory features. See TestPlanGaps_Analysis.md for detailed resolution analysis.

## Scope & Endpoints

- **Upgrade matrix schema and format** — would be resolved by: ADR / design doc defining matrix structure, supported upgrade path syntax, and validation rules
- **odh-cli command signatures and API specification** — would be resolved by: API spec / design doc (Golang implementation confirmed, but command interfaces not specified)
- **Failure attribution mapping implementation details** — would be resolved by: ADR / design doc describing automated attribution algorithm and ownership mapping maintenance
- **Results publication API and consumption interface** — would be resolved by: ADR / API spec defining result schema, publication endpoints, and team consumption mechanisms
- **Bodies of Water integration mechanism** — would be resolved by: ADR / design doc detailing how upgrade validation gates map to BoW transitions and gate pass/fail criteria
- **GitOps tooling specification** — would be resolved by: ADR specifying tool selection (ArgoCD, Flux, etc.) and deployment APIs
- **Pre-upgrade checks API specification** — would be resolved by: API spec / design doc for the validation framework

## Test Strategy & Risks

- **Supported Upgrade Path Definition** — formal confirmation of upgrade paths (2.25→3.5, 3.4→3.5) is missing. The upgrade matrix is critical to test planning. Would be resolved by: feature refinement or ADR defining exact supported upgrade paths.
- **Scenario Contribution Process** — no defined process for how component teams contribute GitOps scenarios, review process, or quality gates for scenarios. Would be resolved by: design doc or component team agreement.
- **Failure Attribution Rules** — acceptance criteria state "failures are attributable to component or platform owners," but the rules for automated attribution are not specified. Would be resolved by: ADR or design doc.
- **Multi-Arch Support Timeline** — Director container blocker is known, but no timeline or workaround plan is documented. Would be resolved by: feature refinement or spike completion under RHOAIENG-60207.
- **Cross-Component Scenario Selection Criteria** — strategy requires "at least one cross-component workflow scenario" per upgrade path but does not define selection criteria or coverage goals. Would be resolved by: design doc or QE requirements document.
- **Pipeline Execution SLOs** — no defined timeout, resource budget, or performance SLO for upgrade validation pipelines. Would be resolved by: design doc or engineering requirements.
- **Manual Upgrade Testing Scope** — strategy states "some manual upgrade testing may still be required" but does not define what remains manual vs automated. Would be resolved by: feature refinement or test strategy document.
- **Ownership Mapping Process and Tooling** — how release engineering maintains component-to-scenario ownership mapping, including automation for ownership updates and staleness detection. Would be resolved by: design doc.
- **Component Scenario Specification Format** — GitOps-based scenario structure, required metadata for ownership and attribution, and how scenarios are discovered and executed by the pipeline. Would be resolved by: design doc.
- **Result Publication Format and Visibility Mechanisms** — result schema, publication endpoints, dashboard or reporting integration, and how release engineering, QE, platform, and component teams consume upgrade validation results. Would be resolved by: design doc.
- **Testing Environment SLOs and Fallback Strategy** — environment provisioning SLOs, monitoring strategy, and fallback environment configuration when primary environment is degraded. Would be resolved by: design doc or ADR.
- **Workload Continuity Measurement Approach** — refinement requires "measure workload continuity and data-plane impact, not only control-plane health" but measurement approach not specified. Would be resolved by: design doc or test specification.

## Environment & Infrastructure

- **Specific OpenShift and RHOAI version pairs for the upgrade matrix** — would be resolved by: feature refinement or ADR defining the exact supported upgrade paths
- **Exact operator versions for each supported component** — would be resolved by: feature refinement or ADR (component dependency matrix)
- **Cluster resource specifications (node count, CPU, memory, storage)** — would be resolved by: ADR or design doc specifying infrastructure requirements per upgrade path
- **Component-specific service account RBAC roles** — would be resolved by: design doc (component-specific RBAC definitions)
- **External service dependencies per component (S3, databases, identity providers)** — would be resolved by: design doc or component scenario documentation listing dependencies
- **Multi-cluster requirement for cross-component scenarios** — would be resolved by: design doc or ADR specifying whether distributed deployments are needed
- **Disconnected upgrade path subset (which upgrade paths must be validated in disconnected mode)** — would be resolved by: feature refinement or ADR defining the disconnected upgrade matrix
- **Disconnected mode implementation specifics** — would be resolved by: design doc detailing which disconnected upgrade paths are supported, how image mirroring is validated, and disconnected mode execution requirements
- **Bodies of Water integration details (which BoW transitions require which upgrade validation gates)** — would be resolved by: ADR or design doc mapping release stages to upgrade stability criteria
- **Namespace-scoped user roles for multi-tenancy testing** — would be resolved by: feature refinement (tenant isolation requirements)
- **Feature flags or feature gates required for upgrade testing** — would be resolved by: design doc (component configuration requirements)
- **operator-chaos pilot tooling details** — would be resolved by: design doc (chaos engineering tool selection and integration)
- **Node count and resource limits per upgrade path** — would be resolved by: design doc (infrastructure sizing for pipeline capacity planning)
- **Discovery spike completion status (4 of 7 in progress under RHOAIENG-60207)** — would be resolved by: feature refinement (pending spike outcomes and architectural decisions)

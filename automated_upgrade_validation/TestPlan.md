---
feature: automated_upgrade_validation
source_key: RHAISTRAT-1519
source_type: strat
version: 1.0.0
status: In Review
author: AI Core Platform QE
components:
- AI Core Platform
additional_docs:
- https://docs.google.com/document/d/.../FeatureRefinement-RHAISTRAT-1519
last_updated: '2026-05-05'
reviewers: []
---
# Automated Upgrade Validation Test Plan
**AI Core Platform QE – Upgrade Pipeline Validation**

**Strategy**: [RHAISTRAT-1519](https://redhat.atlassian.net/browse/RHAISTRAT-1519)

---

## 1. Executive Summary

### 1.1 Purpose
This test plan covers the automated upgrade validation feature for RHOAI, which introduces upgrade validation as a continuous quality gate in CI. The feature ensures that every produced release artifact — nightly, Early Access, or GA — executes the supported upgrade matrix and validates both component-owned and cross-component upgrade scenarios, detecting regressions early and attributing failures to the correct team.

Testing focuses on verifying that the upgrade pipeline correctly deploys pre-upgrade baselines, executes platform upgrades (both connected and disconnected modes), runs post-upgrade validation, and publishes attributable results. Validation must measure workload continuity and data-plane impact, not only control-plane health. Upgrade validation gates are integrated into Bodies of Water transitions, ensuring release-stage promotion requires passing upgrade stability checks. The goal is to replace manual upgrade testing with a reliable automated process that blocks releases when upgrade regressions are detected.

### 1.2 Scope

#### In Scope (AI Core Platform QE Responsibilities)
- Automated validation of all supported upgrade paths for every release artifact (nightly, EA, GA)
- Deployment and validation of component-owned scenarios (pre-upgrade baseline and post-upgrade verification)
- Deployment and validation of cross-component customer workflows (pre-upgrade baseline and post-upgrade verification)
- Platform upgrade execution and post-upgrade step validation
- Pre-upgrade checks and prerequisite resolution validation
- Result publication and failure attribution to component or platform owners
- odh-cli helper functionality for upgrade automation (Golang implementation, CI-tested, documented for manual execution)
- Upgrade matrix expansion without pipeline redesign
- Release-readiness gate integration for Early Access releases
- **Disconnected mode upgrade validation** for the supported disconnected upgrade path set
- **Bodies of Water integration** - upgrade validation gates integrated into BoW transitions for release-stage promotion
- **Workload continuity and data-plane impact measurement** - validate workloads survive upgrades, not just operator convergence

#### Out of Scope (Other Teams)
- Independent component version upgrade testing outside the platform upgrade flow
- Manual upgrade testing beyond what cannot be automated
- Component-specific workload logic (owned by respective component teams)
- Customer-facing upgrade documentation (owned by documentation team)

### 1.3 Test Objectives
1. Verify that every release artifact (nightly, EA, GA) triggers automated validation for all upgrade paths defined in the upgrade matrix.
2. Validate that the upgrade pipeline successfully deploys a cluster at the pre-upgrade version, executes the platform upgrade, and runs required post-upgrade steps in both connected and disconnected modes.
3. Confirm that each supported component has at least one owned validation scenario that passes post-upgrade checks.
4. Verify that each supported upgrade path includes at least one cross-component workflow scenario that remains functional after upgrade.
5. Validate that workload continuity and data-plane impact are measured during upgrades, not only control-plane health (operator convergence).
6. Validate that upgrade failures are correctly attributed to the responsible component or platform owner and that the ownership mapping is maintained.
7. Confirm that upgrade validation results are published automatically and accessible to release engineering and component teams.
8. Verify that upgrade validation gates are integrated into Bodies of Water transitions and function as release-readiness gates for release-stage promotion.
9. Verify that odh-cli provides documented Golang helpers for prerequisite resolution and post-upgrade steps, are continuously exercised in CI, and that customers can reproduce actions manually without requiring the CLI.

---

## 2. Test Strategy

### 2.1 Test Levels
- **Platform Upgrade Integration Testing** — validate full upgrade flow (cluster deployment, pre-upgrade scenarios, platform upgrade execution, post-upgrade validation) across supported version matrices
- **Component-Owned Scenario Testing** — GitOps-based deployment and validation of individual component workloads through upgrade cycles
- **Cross-Component Workflow Testing** — multi-component customer workflow scenarios deployed pre-upgrade and validated post-upgrade to catch integration failures
- **CLI Helper Testing** — validation of odh-cli prerequisite resolution and post-upgrade action helpers
- **CI Pipeline Testing** — validation that every release artifact (nightly, EA, GA) triggers the correct upgrade matrix paths

### 2.2 Test Types
- **Positive Testing** — supported upgrade paths execute successfully with all validation scenarios passing
- **Negative Testing** — upgrade failures are correctly detected and attributed; prerequisite resolution handles missing requirements; invalid upgrade paths are rejected
- **Boundary Testing** — edge cases in upgrade matrix (EUS paths, multi-version skips); large-scale component scenario sets; concurrent upgrade validations
- **Regression Testing** — component workloads that passed pre-upgrade remain functional post-upgrade; cross-component workflows survive platform transitions; existing manual upgrade coverage is not broken

### 2.3 Test Priorities
- **P0 (Critical)** — supported upgrade path fails to complete; component-owned scenario validation fails post-upgrade; cross-component workflow broken after upgrade; release artifact does not trigger upgrade validation; failure attribution is incorrect
- **P1 (High)** — prerequisite resolution fails to handle known dependency; odh-cli helpers produce incorrect results; upgrade matrix addition requires pipeline redesign; results not published or not visible to component teams
- **P2 (Medium)** — manual upgrade documentation is incomplete; optional post-upgrade validation is missing; non-blocking scenario failures; metrics or telemetry gaps

---

## 3. Test Environment

### 3.1 Test Cluster Configuration
- **OpenShift**: OCP 4.20+ (confirmed from initial E2E run)
- **RHOAI versions**: 2.25, 2.25.6, 3.3.2, 3.4, 3.5 (subject to formal upgrade path confirmation)
- **Architecture**: x86_64 (required); multi-arch (arm64/ppc64le/s390x) currently blocked by Director container limitation
- **Cluster provisioning**: must support automated deployment and restoration to pre-upgrade state for multiple test iterations
- **Component operators**: TBD (specific versions depend on component workload specifications)

### 3.2 Test Data Requirements
- **GitOps-based component scenario manifests**: each supported component must provide pre-upgrade baseline workload definitions
- **GitOps-based cross-component workflow manifests**: multi-component integration scenarios (e.g., model serving + data pipeline + monitoring)
- **Upgrade matrix configuration**: supported upgrade paths (2.25→3.5, 3.4→3.5, 2.25.6→3.3.2)
- **Component ownership mapping**: data structure mapping test scenarios to component teams for failure attribution
- **Post-upgrade validation test suites**: executable tests for component-owned and cross-component scenarios
- **odh-cli configuration examples**: documented command sequences for prerequisite resolution and post-upgrade steps

### 3.3 Test Users
- **Cluster admin**: required for operator installation, cluster-wide resource creation, and upgrade execution
- **Component service accounts**: TBD (specific RBAC requirements depend on component workload types)
- **Namespace-scoped users**: TBD (for validating workload isolation and multi-tenancy during upgrades)
- **GitOps controller service accounts**: required permissions for deploying and updating component scenarios

---

## 4. Upgrade Pipeline Steps Under Test

| Endpoint/Method | Type | Purpose | Priority |
|-----------------|------|---------|----------|
| Upgrade pipeline trigger | CI/CD Pipeline | Initiates upgrade validation for a release artifact | P0 |
| Cluster deployment (pre-upgrade version) | Infrastructure | Provisions cluster at baseline version | P0 |
| Component scenario deployment | GitOps | Deploys component-owned workloads as pre-upgrade baseline | P0 |
| Cross-component scenario deployment | GitOps | Deploys multi-component workflows as pre-upgrade baseline | P0 |
| Pre-upgrade checks | Validation | Executes prerequisite checks before upgrade | P0 |
| Prerequisite resolution | CLI/Automation | Resolves identified prerequisites via odh-cli helpers | P1 |
| Platform upgrade execution | Upgrade | Performs RHOAI version upgrade | P0 |
| Post-upgrade steps | Validation | Executes required post-upgrade actions | P0 |
| Component-owned scenario validation | Test Execution | Validates component workloads post-upgrade | P0 |
| Cross-component workflow validation | Test Execution | Validates multi-component workflows post-upgrade | P0 |
| Result publication | Reporting | Publishes validation results to accessible location | P1 |
| Failure attribution | Reporting | Maps failures to component/platform owners | P1 |
| Upgrade matrix configuration | Config | Defines supported upgrade paths | P1 |
| Release-readiness gate check | CI/CD Gate | Blocks EA release if upgrade validation fails | P0 |
| odh-cli helpers (manual-equivalent actions) | CLI | Provides documented upgrade automation helpers | P1 |

---

## 5. Test Cases

**33 test cases** generated across 12 categories.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-PIPE (Pipeline Trigger) | 4 | 3 P0, 1 P1 |
| TC-DEPLOY (Cluster Deployment) | 4 | 3 P0, 1 P1 |
| TC-PRE (Pre-upgrade Checks) | 3 | 2 P0, 1 P1 |
| TC-UPG (Upgrade Execution) | 3 | 2 P0, 1 P2 |
| TC-POST (Post-upgrade Steps) | 3 | 3 P0 |
| TC-COMP (Component Validation) | 3 | 2 P0, 1 P1 |
| TC-XCOMP (Cross-component) | 2 | 2 P0 |
| TC-RPT (Result Publication) | 4 | 3 P1, 1 P2 |
| TC-GATE (Release Gate) | 3 | 2 P0, 1 P1 |
| TC-CLI (odh-cli Helpers) | 4 | 3 P1, 1 P2 |
| TC-MTX (Matrix Extensibility) | 2 | 2 P1 |
| TC-E2E (End-to-End) | 3 | 2 P0, 1 P1 |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- `TC-PIPE` — Upgrade pipeline trigger and orchestration
- `TC-DEPLOY` — Cluster deployment and scenario provisioning
- `TC-PRE` — Pre-upgrade checks and prerequisite resolution
- `TC-UPG` — Platform upgrade execution
- `TC-POST` — Post-upgrade steps and validation
- `TC-COMP` — Component-owned scenario validation
- `TC-XCOMP` — Cross-component workflow validation
- `TC-RPT` — Result publication and failure attribution
- `TC-GATE` — Release-readiness gate checks
- `TC-CLI` — odh-cli helper validation
- `TC-MTX` — Upgrade matrix extensibility
- `TC-E2E` — End-to-end upgrade validation scenarios

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Full upgrade validation cycle for supported upgrade path | All P0 endpoints (pipeline trigger, cluster deployment, scenario deployments, pre-upgrade checks, upgrade execution, post-upgrade steps, component/cross-component validation, result publication, release gate) | P0 |
| TC-E2E-002 | Disconnected mode upgrade validation cycle | Cluster deployment, scenario deployments, pre-upgrade checks, upgrade execution, post-upgrade steps, component/cross-component validation, result publication | P1 |
| TC-E2E-003 | Multi-path upgrade matrix validation with mixed results | Pipeline trigger, upgrade matrix config, result publication, failure attribution, release-readiness gate | P0 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| Upgrade pipeline trigger | TC-E2E-001, TC-E2E-003 |
| Cluster deployment (pre-upgrade version) | TC-E2E-001, TC-E2E-002 |
| Component scenario deployment | TC-E2E-001, TC-E2E-002 |
| Cross-component scenario deployment | TC-E2E-001, TC-E2E-002 |
| Pre-upgrade checks | TC-E2E-001, TC-E2E-002 |
| Prerequisite resolution | TC-E2E-001 |
| Platform upgrade execution | TC-E2E-001, TC-E2E-002 |
| Post-upgrade steps | TC-E2E-001, TC-E2E-002 |
| Component-owned scenario validation | TC-E2E-001, TC-E2E-002 |
| Cross-component workflow validation | TC-E2E-001, TC-E2E-002 |
| Result publication | TC-E2E-001, TC-E2E-002, TC-E2E-003 |
| Failure attribution | TC-E2E-003 |
| Upgrade matrix configuration | TC-E2E-003 |
| Release-readiness gate check | TC-E2E-001, TC-E2E-003 |
| odh-cli helpers | TC-E2E-001 |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

**Applicable** — upgrade validation must cover disconnected mode execution for the supported disconnected upgrade path set so release readiness reflects customer environments that cannot rely on connected registries or external network access during upgrade.

Testing considerations:
- **Pre-upgrade image mirroring** for all component-owned and cross-component scenarios to local registry
- **Operator upgrade execution** without external network access or internet connectivity
- **Validation that odh-cli helpers function correctly** in air-gapped environments
- **Registry access validation** for mirrored upgrade artifacts and catalog sources
- **Disconnected mode upgrade path execution** in the validation pipeline for the supported disconnected upgrade matrix subset

### 7.2 Upgrade/Migration

**Applicable** — this is the core focus of the feature.
- **Upgrade Path Matrix Coverage** — all supported paths (2.25→3.5, 3.4→3.5, etc.) must be validated; adding new paths must not require pipeline redesign
- **Pre-Upgrade State Preservation** — component workloads and cross-component workflows deployed pre-upgrade must survive the platform upgrade and remain functional
- **Post-Upgrade Validation** — automated checks must verify component-owned scenarios and cross-component workflows after upgrade completion
- **Backwards Compatibility** — existing manual upgrade workflows must remain supported; customers must be able to upgrade without odh-cli dependency
- **Data Migration** — if component scenarios include persistent state (models, pipelines, configurations), post-upgrade validation must confirm data integrity
- **Workload Continuity Measurement** — validation must measure workload continuity and data-plane impact (endpoint availability, data integrity, service continuity), not only control-plane health (operator convergence)
- **Rollback Testing** — not in scope (not mentioned in acceptance criteria)

### 7.3 Performance/Scalability

**Applicable** — CI pipeline execution time and resource utilization are critical.
- **Pipeline Execution Time** — full upgrade validation (cluster deploy, pre-upgrade scenarios, upgrade, post-upgrade validation) must complete within acceptable CI/CD timelines; identify bottlenecks in scenario deployment or validation
- **Concurrent Upgrade Validations** — multiple release artifacts (nightly builds, parallel version branches) may trigger upgrade validations concurrently; test resource contention and scheduling
- **Scenario Scale** — large numbers of component-owned scenarios must not cause pipeline timeouts or resource exhaustion
- **Multi-Arch Validation** — Director container is currently x86_64-only; performance implications of adding ARM64 validation paths

### 7.4 RBAC/Authorization

**Not Applicable** — the feature is CI/CD automation infrastructure. The upgrade validation pipeline will run with cluster-admin privileges in ephemeral test clusters. RBAC considerations for customer-facing upgrade workflows are out of scope; those are owned by the platform upgrade process itself, not this automation feature.

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Director container x86_64-only blocks multi-arch validation | High | High (confirmed blocker) | Add multi-arch support to Director container; add timeline to discovery spikes under RHOAIENG-60207; test single-arch validation first |
| Upgrade paths (2.25→3.5, 3.4→3.5) not formally confirmed | High | Medium | Escalate to release engineering and product management for formal upgrade matrix approval; block pipeline implementation until paths are confirmed |
| 4 of 7 discovery spikes under RHOAIENG-60207 still In Progress | Medium | High (confirmed) | Track spike completion; ensure pipeline design does not depend on unresolved spike outcomes; revisit risk assessment when spikes complete |
| Component teams do not provide GitOps-based scenarios | High | Medium | Establish component ownership mapping early; provide scenario templates and documentation; make scenario contribution a release gate |
| Upgrade failures are not attributable to correct team | High | Medium | Define clear attribution rules (component vs platform); implement automated tagging based on failure patterns; validate attribution accuracy with component teams |
| Pipeline cannot handle new upgrade paths without redesign | Medium | Medium | Design upgrade matrix as configuration (not hardcoded); validate extensibility early with test matrix additions; document matrix update process |
| odh-cli helpers are required despite "helper, not a gate" goal | Medium | Medium | Test all upgrade paths without odh-cli; ensure documentation covers manual equivalents; validate customer upgrade workflows do not depend on CLI |
| Cross-component scenario coverage is insufficient | High | Medium | Define minimum cross-component workflow set early; prioritize realistic customer use cases; validate integration failure detection with known historical issues |
| Pipeline execution time exceeds CI/CD budget | Medium | High | Measure baseline execution time for 2.25.6→3.3.2 (already completed at ~28 min); identify parallelization opportunities; set timeout SLOs per upgrade path |
| Results publication format is not actionable | Low | Medium | Define result schema with component teams; ensure failures include logs, component tags, and reproduction steps; validate visibility in team dashboards |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure
- **CI pipeline infrastructure**: capable of triggering validation on every release artifact (nightly, EA, GA)
- **Multi-cluster support**: ability to provision fresh clusters per upgrade path validation
- **Cluster provisioning automation**: deploys clusters at specific RHOAI versions (2.25, 2.25.6, 3.3.2, 3.4, 3.5)
- **Results publication system**: automated storage and visibility of test results for release engineering and component teams
- **Artifact registry**: storage for release artifacts and container images

### 9.2 Configuration
- **Upgrade matrix**: centralized configuration file defining supported upgrade paths without requiring pipeline redesign (declarative format to support path additions)
- **Component scenario registry**: GitOps repository mapping components to their owned validation scenarios
- **Cross-component scenario catalog**: repository of multi-component workflow scenarios
- **odh-cli installation and configuration**: documented environment variables and config files for prerequisite checks and post-upgrade actions (Golang implementation)
- **Operator subscriptions**: catalog sources for RHOAI operators at multiple versions
- **Disconnected mode configuration**: image mirror mappings, catalog source URLs, registry credentials for air-gapped testing
- **Bodies of Water integration configuration**: mapping of upgrade validation gates to BoW transitions for release-stage promotion

### 9.3 Test Tools
- **GitOps tooling**: ArgoCD, Flux, or similar for deploying pre-upgrade scenarios
- **odh-cli**: Golang-based helpers for pre-upgrade checks, prerequisite resolution, post-upgrade steps (continuously exercised in CI, fully documented for manual execution)
- **Kubernetes/OpenShift CLI**: `oc` for cluster operations, upgrade execution, resource validation
- **Automated test execution framework**: for running component-owned and cross-component validation test suites
- **Logging and observability**: tools for capturing upgrade logs, operator logs, component workload logs
- **Failure attribution tooling**: automated analysis to map failures to component or platform owners
- **Image mirroring tools**: `oc adm catalog mirror`, `skopeo` for disconnected mode setup
- **Workload continuity measurement tools**: endpoint monitoring, data integrity verification, service availability checks

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-PIPE | 4 | 3 | 1 | 0 |
| TC-DEPLOY | 4 | 3 | 1 | 0 |
| TC-PRE | 3 | 2 | 1 | 0 |
| TC-UPG | 3 | 2 | 0 | 1 |
| TC-POST | 3 | 3 | 0 | 0 |
| TC-COMP | 3 | 2 | 1 | 0 |
| TC-XCOMP | 2 | 2 | 0 | 0 |
| TC-RPT | 4 | 0 | 3 | 1 |
| TC-GATE | 3 | 2 | 1 | 0 |
| TC-CLI | 4 | 0 | 3 | 1 |
| TC-MTX | 2 | 0 | 2 | 0 |
| TC-E2E | 3 | 2 | 1 | 0 |
| **Total** | **33** | **17** | **12** | **4** |

### 10.2 Upgrade Pipeline Step Coverage

| Endpoint | Test Cases | Coverage |
|----------|------------|----------|
| Upgrade pipeline trigger | TC-PIPE-001, TC-PIPE-002, TC-PIPE-003, TC-PIPE-004, TC-E2E-001, TC-E2E-003 | |
| Cluster deployment (pre-upgrade version) | TC-DEPLOY-001, TC-E2E-001, TC-E2E-002 | |
| Component scenario deployment | TC-DEPLOY-002, TC-DEPLOY-004, TC-E2E-001, TC-E2E-002 | |
| Cross-component scenario deployment | TC-DEPLOY-003, TC-E2E-001, TC-E2E-002 | |
| Pre-upgrade checks | TC-PRE-001, TC-PRE-003, TC-E2E-001, TC-E2E-002 | |
| Prerequisite resolution | TC-PRE-002, TC-CLI-001, TC-E2E-001 | |
| Platform upgrade execution | TC-UPG-001, TC-UPG-002, TC-UPG-003, TC-E2E-001, TC-E2E-002 | |
| Post-upgrade steps | TC-POST-001, TC-CLI-002, TC-E2E-001, TC-E2E-002 | |
| Component-owned scenario validation | TC-COMP-001, TC-COMP-002, TC-COMP-003, TC-POST-002, TC-POST-003, TC-E2E-001, TC-E2E-002 | |
| Cross-component workflow validation | TC-XCOMP-001, TC-XCOMP-002, TC-E2E-001, TC-E2E-002 | |
| Result publication | TC-RPT-001, TC-RPT-002, TC-RPT-004, TC-E2E-001, TC-E2E-002, TC-E2E-003 | |
| Failure attribution | TC-RPT-003, TC-DEPLOY-004, TC-E2E-003 | |
| Upgrade matrix configuration | TC-MTX-001, TC-MTX-002, TC-E2E-003 | |
| Release-readiness gate check | TC-GATE-001, TC-GATE-002, TC-GATE-003, TC-E2E-001, TC-E2E-003 | |
| odh-cli helpers (manual-equivalent actions) | TC-CLI-001, TC-CLI-002, TC-CLI-003, TC-CLI-004, TC-E2E-001 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-05-04 | Initial test plan |

---

**End of Test Plan**

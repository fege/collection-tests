---
feature: evalhub_metrics_discovery
source_key: RHAISTRAT-1507
source_type: strat
version: 1.0.0
status: In Review
author: RHOAI QE
components:
- Model Eval
additional_docs:
- Feature Refinement RHAISTRAT-1507
last_updated: '2026-04-27'
reviewers: []
---
# EvalHub Metrics Discovery Test Plan
**RHOAI QE – EvalHub ServiceMonitor Integration Testing**

**Strategy**: [RHAISTRAT-1507](https://redhat.atlassian.net/browse/RHAISTRAT-1507)

---

## 1. Executive Summary

### 1.1 Purpose

This test plan validates the automatic integration of EvalHub metrics with the OpenShift platform monitoring stack. The EvalHub K8s Controller (in the TrustyAI Service Operator) deploys the EvalHub Service with `prometheus.enabled: true` in the generated ConfigMap, and the EvalHub Service exposes a `/metrics` endpoint. However, the controller's reconciliation loop currently does not create a `ServiceMonitor` custom resource, causing a silent observability failure where EvalHub appears configured for metrics but emits nothing to Prometheus.

The feature introduces three coordinated changes to the TrustyAI Operator's EvalHub reconciliation loop: (1) automatic `ServiceMonitor` creation and lifecycle management tied to the EvalHub CR via `ownerReference`, (2) a `NetworkPolicy` update to permit ingress from the Prometheus scraper to the metrics port, and (3) RBAC extension of the operator's `ClusterRole` with `ServiceMonitor` management permissions. Testing will validate that all three changes work together to ensure metrics are discoverable by Prometheus without manual intervention, that the monitoring configuration follows the EvalHub deployment lifecycle, and that scraping is not silently blocked by network policies. This feature affects 100% of EvalHub OpenShift deployments and is critical for unblocking metric-based alerting and SLO tracking.

### 1.2 Scope

#### In Scope (RHOAI QE Responsibilities)

- Automatic `ServiceMonitor` creation when EvalHub is deployed with metrics enabled (the default)
- `ServiceMonitor` lifecycle management via `ownerReference` to EvalHub CR (creation, update, Kubernetes garbage collection cleanup)
- Prometheus discovery and scraping of the EvalHub `/metrics` endpoint over HTTPS (port named `https`, 30-second scrape interval)
- `NetworkPolicy` update permitting ingress from Prometheus scraper (monitoring namespace) to EvalHub metrics port
- RBAC extension: operator `ClusterRole` with `create, update, patch, delete, get, list, watch` on `monitoring.coreos.com/servicemonitors`
- Graceful degradation when `monitoring.coreos.com/v1` CRD is absent (operator must not block reconciliation)
- Verification that metrics are visible in the OpenShift platform monitoring stack (user-workload-monitoring)
- Cleanup verification when EvalHub instances are deleted (no orphaned `ServiceMonitor` resources)
- Upgrade path validation for existing EvalHub deployments
- TLS configuration validation (`insecureSkipVerify: true` for cluster-internal CA)

#### Out of Scope (Other Teams)

- Grafana dashboard provisioning for EvalHub metrics
- Alerting rule (`PrometheusRule`) definitions and SLO thresholds (proposed as follow-on feature)
- Custom or application-level metrics beyond the HTTP instrumentation already exposed by EvalHub
- `ServiceMonitor` configuration for components not managed by the TrustyAI Operator
- TLS certificate configuration for clusters with custom PKI (beyond `insecureSkipVerify: true`)
- Testing on non-OpenShift Kubernetes distributions

### 1.3 Test Objectives

1. Verify that a `ServiceMonitor` resource is automatically created in the same namespace when an EvalHub CR is deployed with metrics enabled, targeting the EvalHub Service on the HTTPS metrics port at path `/metrics` with a 30-second scrape interval
2. Verify that the `ServiceMonitor` carries an `ownerReference` pointing to the `EvalHub` CR and is automatically deleted via Kubernetes garbage collection when the EvalHub CR is removed
3. Verify that the platform Prometheus instance successfully discovers and scrapes the EvalHub `/metrics` endpoint, with the scrape target showing state `UP`
4. Verify that the operator handles the absence of the `monitoring.coreos.com/v1` CRD gracefully without blocking reconciliation of other EvalHub resources
5. Verify that the `NetworkPolicy` permits ingress from the Prometheus scraper to the EvalHub metrics port, preventing silent scraping failures in namespaces with default-deny ingress policies (e.g., `opendatahub`, `redhat-ods-applications`)
6. Verify that the operator holds the required RBAC permissions (`create, update, patch, delete, get, list, watch` on `monitoring.coreos.com/servicemonitors`) to manage the `ServiceMonitor` lifecycle
7. Verify that platform administrators can define alerting rules against EvalHub metrics and that rules evaluate successfully against live data scraped by Prometheus

---

## 2. Test Strategy

### 2.1 Test Levels

- **API Integration Testing** — Primary focus. Validates Kubernetes API interactions: ServiceMonitor CRUD operations, Prometheus scrape target discovery, and metrics query endpoints
- **Functional Testing** — Validates operator reconciliation logic: ServiceMonitor lifecycle management, `ownerReference` propagation, garbage collection cleanup, and deployment/deletion workflows
- **Data Validation Testing** — Validates ServiceMonitor resource spec correctness: HTTPS port targeting, `/metrics` path, 30-second scrape interval, `insecureSkipVerify: true` TLS config, and `ownerReference` field
- **Security Testing** — Validates NetworkPolicy ingress rules allow Prometheus access, RBAC permissions for ServiceMonitor management, and namespace isolation

### 2.2 Test Types

- **Positive Testing** — Valid deployments with metrics enabled, correct ServiceMonitor creation, successful Prometheus scraping
- **Negative Testing** — Metrics disabled, `monitoring.coreos.com/v1` CRD absent (graceful degradation), NetworkPolicy missing (scraping blocked), RBAC permissions incomplete
- **Boundary Testing** — Multiple EvalHub instances, rapid create/delete cycles, concurrent configuration changes
- **Regression Testing** — Ensure existing EvalHub functionality (model evaluation, service availability) remains intact after operator changes

### 2.3 Test Priorities

- **P0 (Critical)** — ServiceMonitor auto-creation when metrics enabled, Prometheus target discovery with state `UP`, ServiceMonitor deletion via garbage collection when EvalHub CR deleted, NetworkPolicy permitting Prometheus ingress
- **P1 (High)** — RBAC permissions correctly applied in operator ClusterRole, graceful degradation when `monitoring.coreos.com/v1` CRD absent, alerting rule evaluation against live data, `insecureSkipVerify: true` TLS configuration
- **P2 (Medium)** — ServiceMonitor spec correctness (30s interval, HTTPS port, `/metrics` path), concurrent update handling, namespace isolation, backwards compatibility with existing deployments

---

## 3. Test Environment

### 3.1 Test Cluster Configuration

| Component | Version/Requirement |
|-----------|-------------------|
| OpenShift | TBD -- pending RHOAI 3.5.EA1 release compatibility matrix; will be determined by the certified OCP versions listed in the RHOAI 3.5.EA1 release notes. Requires user-workload-monitoring enabled |
| RHOAI | 3.5.EA1 or later |
| TrustyAI Service Operator | TBD -- version will be determined by the RHOAIENG-58889 build output containing the ServiceMonitor feature |
| Prometheus Operator | Bundled with OpenShift platform monitoring, `monitoring.coreos.com/v1` CRD available |
| EvalHub Service | Version matching RHOAI 3.5.EA1, metrics endpoint on port named `https` |
| User-workload monitoring | `enableUserWorkload: true` in `cluster-monitoring-config` ConfigMap |
| Namespace isolation | NetworkPolicy enforcement enabled in EvalHub namespace (e.g., `opendatahub`, `redhat-ods-applications`) |

### 3.2 Test Data Requirements

- **EvalHub CR (metrics enabled)**: Valid custom resource to trigger operator reconciliation and ServiceMonitor creation (metrics enabled is the default). Minimal sample:
  ```yaml
  apiVersion: trustyai.opendatahub.io/v1
  kind: EvalHub
  metadata:
    name: evalhub-sample
    namespace: opendatahub
  spec:
    prometheus:
      enabled: true
  ```
  > **Note**: The exact CRD schema and apiVersion are TBD -- will be confirmed from the RHOAIENG-58889 build artifacts. The above is a minimal placeholder based on existing TrustyAI CRD conventions.
- **EvalHub CR (metrics disabled)**: Custom resource with `prometheus.enabled: false` for negative test scenarios
- **Expected ServiceMonitor manifest**: ServiceMonitor YAML with `ownerReference` to EvalHub CR, HTTPS port, 30-second scrape interval, `/metrics` path, `insecureSkipVerify: true` (per Feature Refinement RHAISTRAT-1507)
- **NetworkPolicy sample**: Ingress rule allowing traffic from monitoring namespace (`openshift-user-workload-monitoring`) to EvalHub metrics port
- **ClusterRole RBAC manifest**: Extended permissions for `monitoring.coreos.com/servicemonitors` (`create, update, patch, delete, get, list, watch`)
- **Prometheus query samples**: PromQL queries to verify metrics are being scraped (e.g., `up{job="evalhub"}`, EvalHub-specific HTTP instrumentation metrics). Expected metric names TBD -- will be confirmed from the EvalHub `/metrics` endpoint output once the RHOAIENG-58889 build is available
- **PrometheusRule sample**: Example alerting rule YAML for acceptance criterion #3 validation

### 3.3 Test Users

| User/Account | Role | Purpose |
|-------------|------|---------|
| Cluster admin | `cluster-admin` | Enable user-workload-monitoring, manage platform monitoring configuration |
| Namespace admin | `admin` (namespace-scoped) | Deploy EvalHub instances, verify ServiceMonitor lifecycle and cleanup |
| TrustyAI operator SA | `ServiceAccount` | Operator service account with extended `ClusterRole` for ServiceMonitor CRUD |
| Prometheus SA | `ServiceAccount` (from `openshift-user-workload-monitoring`) | Verify NetworkPolicy permits scraping of `/metrics` endpoint |
| Restricted user | No `ServiceMonitor` permissions | Verify RBAC enforcement — cannot create/modify/delete ServiceMonitor resources |

---

## 4. Endpoints/Methods Under Test

| Endpoint/Method | Type | Purpose | Priority | Source |
|-----------------|------|---------|----------|--------|
| `/metrics` | REST | EvalHub HTTP instrumentation metrics endpoint (HTTPS, port named `https`) | P0 | Strategy (RHAISTRAT-1507): `/metrics` endpoint. Port named `https` and HTTPS protocol per Feature Refinement RHAISTRAT-1507 |
| `ServiceMonitor` resource | K8s Resource | Prometheus scrape target definition with `ownerReference` to EvalHub CR, 30s interval, `/metrics` path | P0 | Strategy: ServiceMonitor concept. `ownerReference` and 30s scrape interval per Feature Refinement RHAISTRAT-1507 |
| `NetworkPolicy` resource | K8s Resource | Ingress rule permitting Prometheus scraper access to EvalHub metrics port | P0 | Feature Refinement RHAISTRAT-1507 |
| `ClusterRole` resource | K8s Resource | RBAC role granting operator `create, update, patch, delete, get, list, watch` on `monitoring.coreos.com/servicemonitors` | P0 | Feature Refinement RHAISTRAT-1507 (RBAC permissions detail) |
| TrustyAI Service Operator reconciliation loop | Controller Method | Controller logic that creates/updates/deletes ServiceMonitor based on EvalHub state | P0 | Strategy: "the controller's reconciliation loop does not create a ServiceMonitor" |
| Prometheus scrape target (state `UP`) | Config | Platform Prometheus instance discovery of EvalHub as active scrape target | P0 | Strategy AC1: "scraped by the platform Prometheus instance without manual configuration" |
| `monitoring.coreos.com/v1` CRD presence check | Controller Method | Operator logic to detect CRD availability and degrade gracefully if absent | P1 | Feature Refinement RHAISTRAT-1507 (graceful degradation) |
| `insecureSkipVerify: true` TLS config | Config | TLS setting for compatibility with OpenShift cluster-internal CA | P1 | Feature Refinement RHAISTRAT-1507 (TLS configuration detail) |
| Prometheus query API (`/api/v1/query`) | REST | Verify metrics are queryable after scraping | P1 | Extrapolated from Strategy AC3: "alerting rules and SLO tracking" |
| Alerting rule evaluation | Config | Platform administrator `PrometheusRule` targeting EvalHub metrics | P1 | Strategy AC3: "Operators can set up alerting rules and SLO tracking" |

---

## 5. Test Cases

**22 test cases** generated across 8 categories.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-SM (ServiceMonitor Lifecycle) | 5 | 3 P0, 2 P1 |
| TC-SCRAPE (Prometheus Scraping) | 3 | 1 P0, 1 P1, 1 P2 |
| TC-NP (NetworkPolicy) | 2 | 2 P0 |
| TC-RBAC (RBAC Permissions) | 3 | 2 P1, 1 P2 |
| TC-CFG (Configuration) | 2 | 1 P1, 1 P2 |
| TC-NEG (Negative Testing) | 3 | 3 P1 |
| TC-UPGRADE (Upgrade/Migration) | 3 | 3 P2 |
| TC-E2E (End-to-End) | 3 | 3 P0 |
| **Total** | **22** | **9 P0, 8 P1, 5 P2** |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

| Prefix | Category |
|--------|----------|
| `TC-SM` | ServiceMonitor lifecycle (creation, update, deletion, ownerReference) |
| `TC-SCRAPE` | Prometheus scraping and metric discovery |
| `TC-NP` | NetworkPolicy ingress rules for Prometheus access |
| `TC-RBAC` | RBAC permissions for ServiceMonitor management |
| `TC-CFG` | Configuration management (enable/disable, TLS, scrape interval) |
| `TC-NEG` | Negative testing (CRD absent, NetworkPolicy missing, RBAC incomplete) |
| `TC-UPGRADE` | Upgrade and migration scenarios |
| `TC-E2E` | End-to-end scenarios |

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Deploy EvalHub → ServiceMonitor created → Prometheus scrapes → metrics queryable | `/metrics`, ServiceMonitor, Reconciliation loop, Prometheus target UP, Prometheus query API | P0 |
| TC-E2E-002 | NetworkPolicy and ServiceMonitor ship together for scraping in default-deny namespace | NetworkPolicy, ServiceMonitor, `/metrics`, Prometheus target UP | P0 |
| TC-E2E-003 | Deploy EvalHub → delete EvalHub → ServiceMonitor cleaned up | ServiceMonitor, Reconciliation loop, Prometheus target UP | P0 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| `/metrics` | TC-E2E-001, TC-E2E-002 |
| `ServiceMonitor` resource | TC-E2E-001, TC-E2E-002, TC-E2E-003 |
| `NetworkPolicy` resource | TC-E2E-002 |
| `ClusterRole` resource | TC-E2E-001 (implicit) |
| TrustyAI Service Operator reconciliation loop | TC-E2E-001, TC-E2E-003 |
| Prometheus scrape target (state `UP`) | TC-E2E-001, TC-E2E-002, TC-E2E-003 |
| `monitoring.coreos.com/v1` CRD presence check | — |
| `insecureSkipVerify: true` TLS config | TC-E2E-001 (implicit) |
| Prometheus query API (`/api/v1/query`) | TC-E2E-001 |
| Alerting rule evaluation | — |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

**Not Applicable** — The ServiceMonitor is a platform CRD managed by the Prometheus Operator bundled with OpenShift. The feature does not interact with external registries, image pulls, or network-dependent resources at runtime. The operator creates a Kubernetes resource within the cluster; no external network access is required for ServiceMonitor creation or Prometheus scraping of the local `/metrics` endpoint.

### 7.2 Upgrade/Migration

Testing considerations for upgrades and migrations are critical for this feature:

- **Backwards compatibility**: Verify that existing EvalHub deployments (deployed before the ServiceMonitor feature) continue to function after operator upgrade. Metrics remain inaccessible until EvalHub CR reconciliation triggers ServiceMonitor creation.
- **Operator upgrade path**: Validate that upgrading the TrustyAI Service Operator from a version without ServiceMonitor support to the new version triggers reconciliation and creates ServiceMonitors for all existing EvalHub instances with metrics enabled
- **ClusterRole RBAC update**: Test that the operator's `ClusterRole` is correctly updated during upgrade to include `monitoring.coreos.com/servicemonitors` permissions. Missing permissions must not block existing EvalHub functionality.
- **Rollback safety**: Confirm that downgrading the operator does not leave orphaned ServiceMonitor resources. Verify that `ownerReference`-based garbage collection handles rollback correctly.
- **CRD schema stability**: Test against `monitoring.coreos.com/v1` CRD schema changes. Operator must handle field additions/deprecations gracefully.

### 7.3 Performance/Scalability

- **Scrape interval impact**: Verify the 30-second scrape interval does not degrade EvalHub response time under load. Test `/metrics` endpoint latency with concurrent Prometheus scrapes.
- **Prometheus target discovery latency**: Measure time from ServiceMonitor creation to Prometheus target state transition (unknown to `UP`). Should complete within 90 seconds (3x scrape interval).
- **Controller resource consumption**: Monitor CPU and memory usage of the TrustyAI Service Operator during ServiceMonitor reconciliation at scale (multiple EvalHub CRs per namespace, multiple namespaces).
- **Multi-tenant scaling**: Validate correct behavior when multiple EvalHub instances exist across different namespaces, ensuring each gets its own correctly-scoped ServiceMonitor and NetworkPolicy.

### 7.4 RBAC/Authorization

- **Operator ClusterRole permissions**: Verify `create, update, patch, delete, get, list, watch` on `monitoring.coreos.com/servicemonitors` succeed. Test permission denial when ClusterRole is incomplete.
- **Namespace-scoped ServiceMonitor isolation**: Verify ServiceMonitor in one namespace does not affect EvalHub in another namespace. Test multi-tenant scenarios with strict namespace isolation.
- **Prometheus service account permissions**: Verify OpenShift user-workload-monitoring service account can scrape EvalHub metrics without additional RBAC grants. Test scraping fails when NetworkPolicy ingress rules are missing.
- **Privilege escalation prevention**: Verify operator cannot create ServiceMonitor targeting non-EvalHub endpoints or cross-namespace scrape targets.

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| `monitoring.coreos.com/v1` CRD absent on dev/test clusters blocks operator reconciliation | High | Medium | Implement CRD presence check in operator startup. Degrade gracefully: log warning, skip ServiceMonitor creation, continue EvalHub reconciliation. Add test case with CRD absent. |
| NetworkPolicy not updated in `opendatahub` and `redhat-ods-applications` namespaces silently blocks Prometheus scraping | High | High | Ship NetworkPolicy update and ServiceMonitor creation as atomic change. Add integration test verifying scrape success in default-deny namespace. |
| OpenShift user-workload-monitoring disabled (`enableUserWorkload: false`) causes target discovery failure | High | Medium | Add operator precheck: query `cluster-monitoring-config` ConfigMap for `enableUserWorkload: true`. Log error if disabled. Document prerequisite. |
| `insecureSkipVerify: true` required for OpenShift internal CA may fail with custom PKI | Medium | Low | Default to `insecureSkipVerify: true`. Document custom CA configuration via ServiceMonitor `tlsConfig.ca` field. Add test case for custom CA scenario (manual verification only). |
| Concurrent EvalHub CR updates cause ServiceMonitor spec drift (stale scrape config) | Medium | Low | Implement optimistic locking in operator reconciliation. Use `resourceVersion` comparison before ServiceMonitor updates. Add concurrency test. |
| Alerting rule evaluation depends on external PrometheusRule CR creation (follow-on work) | Medium | High | Provide example PrometheusRule YAML as test fixture. Acceptance Criterion #3 becomes manual verification step until PrometheusRule automation ships. |
| ClusterRole RBAC update during operator upgrade may fail on RBAC-restricted clusters | Low | Low | Add test for ClusterRole update. Document manual RBAC grant procedure for restricted environments. Operator logs permission denial errors with remediation steps. |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure

| Requirement | Details |
|-------------|---------|
| OpenShift cluster | Single cluster with Prometheus Operator and user-workload-monitoring enabled |
| RHOAI operator | Installed with Model Eval (EvalHub K8s Controller) component |
| Monitoring namespace | `openshift-user-workload-monitoring` for Prometheus scraper pods |
| EvalHub namespace | `opendatahub` or `redhat-ods-applications` with NetworkPolicy enforcement enabled |
| Persistent Prometheus | Platform instance for scraping user-workload metrics |
| Multi-tenant namespaces | At least 2 namespaces for namespace isolation testing |

### 9.2 Configuration

| Configuration | Details |
|---------------|---------|
| `cluster-monitoring-config` | `enableUserWorkload: true` in `openshift-monitoring` namespace |
| ClusterRole | Extended with `monitoring.coreos.com/servicemonitors` permissions |
| ServiceMonitor | Namespace-scoped, `ownerReference` to EvalHub CR, HTTPS port `https`, scrape interval 30s, path `/metrics`, `insecureSkipVerify: true` |
| NetworkPolicy | Ingress rule from monitoring namespace pods to EvalHub metrics port |
| EvalHub CR | `prometheus.enabled: true` (default) or `false` for negative tests |
| Build process | [RHOAIENG-58889](https://issues.redhat.com/browse/RHOAIENG-58889) for operator build with ServiceMonitor feature. The testable operator image will be published as a build artifact of this engineering issue. |

### 9.3 Test Tools

| Tool | Purpose |
|------|---------|
| `oc` / `kubectl` | Resource management, ServiceMonitor CRUD, RBAC verification, NetworkPolicy inspection |
| `curl` / `httpie` | Direct HTTPS requests to EvalHub `/metrics` endpoint |
| `promtool` | Prometheus scrape config and rule validation |
| `jq` / `yq` | YAML/JSON parsing for ServiceMonitor, `ownerReference`, NetworkPolicy inspection |
| `oc adm policy` | Verify ClusterRole permissions for operator service account |
| `oc get events` | Debug ServiceMonitor creation failures or CRD availability issues |
| Prometheus UI (Thanos Querier) | Verify metrics ingested and queryable (`up{job="evalhub"}`) |

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-SM | 5 | 3 | 2 | 0 |
| TC-SCRAPE | 3 | 1 | 1 | 1 |
| TC-NP | 2 | 2 | 0 | 0 |
| TC-RBAC | 3 | 0 | 2 | 1 |
| TC-CFG | 2 | 0 | 1 | 1 |
| TC-NEG | 3 | 0 | 3 | 0 |
| TC-UPGRADE | 3 | 0 | 0 | 3 |
| TC-E2E | 3 | 3 | 0 | 0 |
| **Total** | **22** | **9** | **8** | **5** |

### 10.2 Endpoint/Method Coverage

| Endpoint/Method | Test Cases | Coverage |
|-----------------|------------|----------|
| `/metrics` | TC-SM-001, TC-SCRAPE-001, TC-NP-002, TC-E2E-001, TC-E2E-002 | |
| `ServiceMonitor` resource | TC-SM-001, TC-SM-002, TC-SM-003, TC-SM-004, TC-SM-005, TC-CFG-002, TC-E2E-001, TC-E2E-003 | |
| `NetworkPolicy` resource | TC-NP-001, TC-NP-002, TC-NEG-002, TC-E2E-002 | |
| `ClusterRole` resource | TC-RBAC-001, TC-RBAC-002, TC-UPGRADE-002 | |
| TrustyAI Service Operator reconciliation loop | TC-SM-001, TC-SM-003, TC-SM-004, TC-NEG-001, TC-E2E-001, TC-E2E-003 | |
| Prometheus scrape target (state `UP`) | TC-SCRAPE-001, TC-SCRAPE-003, TC-NP-002, TC-E2E-001, TC-E2E-002 | |
| `monitoring.coreos.com/v1` CRD presence check | TC-NEG-001 | |
| `insecureSkipVerify: true` TLS config | TC-CFG-001 | |
| Prometheus query API (`/api/v1/query`) | TC-SCRAPE-002, TC-E2E-001 | |
| Alerting rule evaluation | — (manual verification with PrometheusRule fixture) | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-27 | Initial test plan |

---

**End of Test Plan**

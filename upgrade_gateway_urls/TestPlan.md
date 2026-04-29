---
feature: upgrade_gateway_urls
source_key: RHOAIENG-48747
source_type: issue
version: 1.0.0
status: In Review
author: RHOAI QE
components: []
additional_docs: []
last_updated: '2026-04-28'
reviewers: []
---
# Upgrade Gateway URLs Test Plan
**RHOAI QE – Workbench URL Accessibility After 2.x to 3.x Upgrade**

**Strategy**: [RHOAIENG-48747](https://redhat.atlassian.net/browse/RHOAIENG-48747)

---

## 1. Executive Summary

### 1.1 Purpose
This test plan validates that workbenches created in RHOAI 2.x remain accessible through the Dashboard after upgrading to RHOAI 3.x. The upgrade introduces a new Gateway-based routing model (HTTPRoutes) replacing the legacy OpenShift Route model. A confirmed bug causes the 3.x controller to create HTTPRoutes targeting service port 8888 for unmigrated workbenches, while the base notebook service only exposes port 80 (with targetPort 8888), resulting in HTTP 500 errors when users click workbench URLs in the Dashboard.

Testing ensures that the fix correctly handles both migrated and unmigrated workbenches, that legacy Route-based URLs continue to function, and that the Dashboard displays working URLs regardless of workbench migration status.

### 1.2 Scope

#### In Scope (RHOAI QE Responsibilities)
- Dashboard URL display logic for workbenches during and after upgrade from 2.x to 3.x
- HTTPRoute creation and configuration by the 3.x controller for unmigrated workbenches
- Service port configuration for base notebook containers (unmigrated workbenches without `inject-auth: true`)
- Workbench accessibility via legacy Route-based URLs (both with and without path components)
- Workbench accessibility via new Gateway-based URLs post-upgrade
- HTTP status validation (500 error reproduction and resolution)
- Workbench migration to new authentication model (`inject-auth: true`)
- URL behavior for multiple workbench types: JupyterLab, Code-server, RStudio

#### Out of Scope (Other Teams)
- Creation and management of workbenches in RHOAI 2.x environments (pre-upgrade)
- General workbench functionality unrelated to URL accessibility
- The upgrade process itself (only testing post-upgrade state)
- Gateway API controller internals (Istio/Envoy routing logic)
- OpenShift Route controller behavior

### 1.3 Test Objectives
1. Verify that unmigrated 2.x workbenches display accessible URLs in the Dashboard after upgrade to 3.x
2. Reproduce HTTP 500 errors when accessing unmigrated workbenches via new Gateway-based URLs (regression baseline)
3. Validate that legacy Route-based URLs continue to work for Code-server, RStudio, and JupyterLab workbenches post-upgrade
4. Verify HTTPRoute configuration for unmigrated workbenches (port targeting, Accepted status)
5. Confirm service port configuration for base notebook containers (port 80 with targetPort 8888)
6. Test workbench migration to `inject-auth: true` and validate Gateway-based URL accessibility post-migration
7. Ensure Dashboard URL logic correctly switches between legacy Routes and new HTTPRoutes based on workbench migration status

---

## 2. Test Strategy

### 2.1 Test Levels
- **Upgrade/Migration Testing** — Primary focus; validates workbench URL accessibility across the 2.25 to 3.3 upgrade boundary
- **API Integration Testing** — Validates HTTP responses (200 vs 500) from both Route-based and Gateway-based URLs
- **UI Testing** — Verifies Dashboard displays correct and functional workbench URLs
- **Functional Testing** — Tests port configuration, HTTPRoute creation, and migration state transitions
- **Data Validation Testing** — Confirms Kubernetes resource configurations (Service ports, HTTPRoute specs, annotations)

### 2.2 Test Types
- **Positive Testing** — Migrated workbenches accessible via Gateway URLs; unmigrated workbenches accessible via legacy Route URLs; Dashboard shows correct URLs per migration status
- **Negative Testing** — Unmigrated workbenches return 500 via Gateway URLs (pre-fix regression); invalid port configurations detected
- **Boundary Testing** — Mixed-state environments (some workbenches migrated, some not); all three workbench types (JupyterLab, Code-server, RStudio); namespaces with multiple workbenches
- **Regression Testing** — Ensure fix does not break newly created 3.x workbenches; verify existing workbench functionality is preserved

### 2.3 Test Priorities
- **P0 (Critical)** — Workbench URLs in Dashboard return HTTP 200 (not 500) for all workbench types after upgrade; legacy Route-based URLs remain functional
- **P1 (High)** — Dashboard correctly displays Gateway vs Route URLs based on migration status; workbench migration to `inject-auth: true` produces working Gateway URLs
- **P2 (Medium)** — HTTPRoute status fields accurately reflect routing health; Dashboard handles edge cases (stopped workbenches, deleted routes)

---

## 3. Test Environment

### 3.1 Test Cluster Configuration
- **OpenShift version**: OCP 4.16 or later (TBD — pending release compatibility matrix for RHOAI 3.3; Gateway API CRDs require OCP 4.16+ with the Gateway API feature gate enabled)
- **Cluster sizing**: TBD — pending engineering guidance on minimum resource requirements; recommend at least 3 worker nodes with 16 GiB RAM and 4 vCPUs each to support simultaneous workbench pods during upgrade testing
- OpenShift cluster with Gateway API support (Gateway API CRDs and controllers installed; verify CRDs are present via `oc get crd gateways.gateway.networking.k8s.io`)
- RHOAI 2.25 initially installed with workbenches created pre-upgrade
- RHOAI 3.3 upgrade path (in-place operator upgrade via OLM)
- Route infrastructure preserved from 2.x for legacy URL verification

### 3.2 Test Data Requirements
- Pre-existing workbenches created in RHOAI 2.25 **before** the upgrade to 3.3. Creation steps:
  1. Create a Data Science Project (DSP) in the Dashboard (e.g., `upgrade-url-test`)
  2. Within the DSP, create workbenches of each type using the default notebook images shipped with RHOAI 2.25:
     - **JupyterLab**: Select the "Standard Data Science" notebook image; name the workbench (e.g., `jupyter-wb`); container size Small (2 CPU / 8 GiB); requires `/notebook/[namespace]/[notebook]` path for access
     - **Code-server**: Select the "code-server" notebook image (TBD — pending confirmation of exact image name in RHOAI 2.25 image list); name the workbench (e.g., `codeserver-wb`); container size Small; no URL path required
     - **RStudio**: Select the "RStudio Server" notebook image (TBD — pending confirmation of exact image name in RHOAI 2.25 image list); name the workbench (e.g., `rstudio-wb`); container size Small; no URL path required
  3. Start each workbench and confirm it reaches "Running" status in the Dashboard before proceeding with upgrade
  4. Verify each workbench is accessible via its Route URL before the upgrade (baseline sanity check)
- Workbenches in various states:
  - Without `inject-auth: true` annotation (unmigrated) — this is the default state for workbenches created in 2.25
  - With `inject-auth: true` annotation (migrated, for comparison) — manually annotate one workbench post-upgrade via `oc annotate notebook <name> inject-auth=true` to test the migrated path
- Service configurations with port 80 exposed (targetPort 8888) for base notebooks

### 3.3 Test Users
- **Admin user** — `cluster-admin` role (or equivalent). Permissions required: create Data Science Projects and workbenches in RHOAI 2.25, manage RHOAI operator subscriptions for the upgrade from 2.25 to 3.3, inspect HTTPRoute/Service/Route resources via `oc` CLI, annotate Notebook CRs. Credentials: TBD — pending test environment provisioning; use `kubeadmin` or a dedicated service account with `cluster-admin` ClusterRoleBinding.
- **Regular user** — Member of the Data Science Project with `edit` role on the project namespace. Permissions required: view workbench URLs in Dashboard, access workbenches via both Gateway and Route URLs, verify workbench functionality after upgrade. Credentials: TBD — pending test environment provisioning; create via `oc create user` and bind with `oc adm policy add-role-to-user edit <user> -n <namespace>`.

---

## 4. Endpoints/Resources Under Test

| Endpoint/Resource | Type | Purpose | Priority |
|-------------------|------|---------|----------|
| `https://[notebook]-[namespace].apps.[cluster]/` | Route URL | Legacy URL for Code-server/RStudio workbenches (no path) | P0 |
| `https://[notebook]-[namespace].apps.[cluster]/notebook/[namespace]/[notebook]` | Route URL | Legacy URL for JupyterLab workbenches (with path) | P0 |
| Dashboard workbench URL display | UI Component | Shows clickable workbench access URLs | P0 |
| HTTPRoute resource for unmigrated workbenches | K8s Resource | Gateway-based routing configuration created by 3.x controller | P0 |
| Notebook Service port configuration | K8s Service | Service port 80 with targetPort 8888 for base notebooks | P0 |
| Workbench `inject-auth` annotation | K8s Metadata | Indicates migration status to new authentication model | P1 |
| Migrated workbench accessibility via Gateway (derived from migration path in strategy) | HTTPRoute URL | Verifies Gateway-based URLs work after workbench migration to `inject-auth: true` | P1 |

---

## 5. Test Cases

**18 test cases** generated across 5 categories. Upgrade scenario: bug discovered during upgrade. All 18 TCs carry `upgrade_phase` tags (10 `both`, 8 `post`) — `both` TCs establish pre-upgrade PASS baselines for regression detection. See [test_cases/INDEX.md](test_cases/INDEX.md) for the full index.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-URL (URL Accessibility) | 4 | P0: 3, P1: 1 |
| TC-UI (Dashboard Display) | 4 | P0: 1, P1: 2, P2: 1 |
| TC-CFG (K8s Configuration) | 4 | P0: 2, P1: 2 |
| TC-UPG (Upgrade/Migration) | 3 | P0: 1, P1: 2 |
| TC-E2E (End-to-End) | 3 | P0: 3 |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- `TC-UPG` — Upgrade and migration scenarios (pre/post upgrade state validation)
- `TC-URL` — URL accessibility and HTTP response validation
- `TC-UI` — Dashboard URL display and interaction
- `TC-CFG` — Kubernetes resource configuration validation (HTTPRoute, Service, annotations)
- `TC-E2E` — End-to-end upgrade and workbench access scenarios

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Full upgrade journey — pre-upgrade baseline and post-upgrade workbench accessibility | Route URLs (Code-server/RStudio, JupyterLab), Dashboard URL display | P0 |
| TC-E2E-002 | Migration journey — upgrade, migrate workbench, verify Gateway URLs | Service port config, HTTPRoute, inject-auth annotation, Gateway URL, Dashboard URL display | P0 |
| TC-E2E-003 | Mixed-state cluster — all workbench types and migration states | Route URLs, Gateway URL, Dashboard URL display, inject-auth annotation | P0 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| `https://[notebook]-[namespace].apps.[cluster]/` (Route, Code-server/RStudio) | TC-E2E-001, TC-E2E-003 |
| `https://[notebook]-[namespace].apps.[cluster]/notebook/[namespace]/[notebook]` (Route, JupyterLab) | TC-E2E-001, TC-E2E-003 |
| Dashboard workbench URL display | TC-E2E-001, TC-E2E-002, TC-E2E-003 |
| HTTPRoute resource for unmigrated workbenches | TC-E2E-002 |
| Notebook Service port configuration | TC-E2E-002 |
| Workbench `inject-auth` annotation | TC-E2E-002, TC-E2E-003 |
| Migrated workbench accessibility via Gateway | TC-E2E-002, TC-E2E-003 |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

**Not Applicable** — This issue involves internal cluster routing between the Dashboard, HTTPRoutes, and notebook services. It does not interact with external registries, image pulls, or network-dependent resources at runtime. The port mismatch is a cluster-internal configuration issue.

### 7.2 Upgrade/Migration

This is the central concern of the test plan. Testing must cover:
- **Backwards compatibility**: Unmigrated 2.x workbenches must remain accessible via legacy Route URLs after upgrade to 3.x
- **State migration**: Workbenches transitioning from unmigrated (`inject-auth` absent) to migrated (`inject-auth: true`) must have Gateway URLs updated to target the correct service port
- **Mixed-state environments**: Clusters with both migrated and unmigrated workbenches must handle each correctly
- **Rollback considerations**: If a migration fails partway, the workbench should still be accessible via at least one URL path
- **Operator upgrade path**: RHOAI 2.25 to 3.3 in-place upgrade via OLM must not orphan or corrupt workbench routing resources

### 7.3 Performance/Scalability

**Not Applicable** — This is a correctness bug involving port mismatch in HTTPRoute configuration. There is no user-facing latency path or data-volume-dependent behavior specific to this issue. Standard workbench access latency is covered by existing performance testing.

### 7.4 RBAC/Authorization

Testing should verify:
- **Pre-migration auth model**: Unmigrated workbenches use the legacy authentication path via OpenShift Routes; users with existing access should retain it post-upgrade
- **Post-migration auth model**: Migrated workbenches using `inject-auth: true` route through the Gateway with the new authentication model; user permissions must be enforced correctly
- **Dashboard permissions**: Users should only see URLs for workbenches they have access to, regardless of migration status
- **Service account rights**: The 3.x controller service account must have permissions to create and modify HTTPRoute resources in workbench namespaces

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Unmigrated workbenches remain inaccessible via Dashboard URLs after fix | High | High | Validate with all three workbench types (JupyterLab, Code-server, RStudio) across upgrade; test both Route and Gateway URL paths |
| Automatic migration causes data loss or workbench downtime | High | Medium | Test migration on non-production cluster first; verify workbench state preservation before and after migration |
| Port mismatch behavior varies by workbench type | High | Medium | Test each workbench type independently; validate service port and HTTPRoute configuration per type |
| Fix breaks newly created 3.x workbenches | High | Low | Include regression tests for 3.x-native workbench creation and access |
| HTTPRoute status shows "Accepted" but traffic still fails | Medium | High | Validate actual HTTP response codes, not just Kubernetes resource status; implement health checks beyond status fields |
| Mixed migrated/unmigrated state causes Dashboard rendering issues | Medium | Medium | Test clusters with workbenches in both states; verify Dashboard handles mixed states gracefully |
| Legacy Route URLs stop working after upgrade | Medium | Low | Explicitly test all documented workaround URLs post-upgrade |
| Cached or bookmarked Gateway URLs cause user confusion after fix | Low | Medium | Document URL change behavior; verify Dashboard updates URLs after migration |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure
- Single OpenShift cluster running OCP 4.16 or later (TBD — pending release compatibility matrix for RHOAI 3.3)
- Cluster sizing: at least 3 worker nodes, each with 16 GiB RAM and 4 vCPUs (TBD — pending engineering guidance on minimum resource requirements for concurrent workbench pods during upgrade)
- RHOAI operator with upgrade capability between versions (OLM subscription)
- Gateway API CRDs and controllers installed and functional (verify via `oc get crd gateways.gateway.networking.k8s.io`)
- Existing Route infrastructure preserved through upgrade
- Workbench controller components for both 2.x and 3.x architectures

### 9.2 Configuration
- **RHOAI 2.25 (pre-upgrade)**: Install from the `stable` operator channel (TBD — pending confirmation of exact channel name and catalog source version for 2.25; catalog source: `redhat-operators` in `openshift-marketplace` namespace)
- **RHOAI 3.3 (post-upgrade)**: Upgrade by switching the operator subscription to the 3.x channel (TBD — pending confirmation of exact channel name; e.g., `stable-3.x` or `fast`; catalog source: `redhat-operators`)
- Upgrade procedure: Edit the RHOAI Subscription resource to point to the 3.3 channel and approve the InstallPlan (automatic or manual approval per cluster policy)
- Workbench CR configurations without `inject-auth: true` annotation (unmigrated state)
- Service configurations exposing port 80 with targetPort 8888
- HTTPRoute configurations created by 3.x controller
- Dashboard configuration to display workbench URLs

### 9.3 Test Tools
- `oc` CLI (v4.16+) for inspecting Kubernetes resources (Routes, HTTPRoutes, Services, annotations)
- `curl` or `httpie` for testing URL accessibility and HTTP response codes
- Web browser for Dashboard URL verification and workbench access
- `kubectl` for detailed resource inspection and port-forward debugging
- Log viewing tools (`oc logs`) to debug 500 errors and port mismatch issues

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-URL | 4 | 3 | 1 | 0 |
| TC-UI | 4 | 1 | 2 | 1 |
| TC-CFG | 4 | 2 | 2 | 0 |
| TC-UPG | 3 | 1 | 2 | 0 |
| TC-E2E | 3 | 3 | 0 | 0 |
| **Total** | **18** | **10** | **7** | **1** |

### 10.2 Endpoint/Resource Coverage

| Endpoint/Resource | Test Cases | Coverage |
|-------------------|------------|----------|
| `https://[notebook]-[namespace].apps.[cluster]/` (Route, Code-server/RStudio) | TC-URL-001, TC-UPG-001, TC-E2E-001, TC-E2E-003 | |
| `https://[notebook]-[namespace].apps.[cluster]/notebook/[namespace]/[notebook]` (Route, JupyterLab) | TC-URL-002, TC-UPG-001, TC-E2E-001, TC-E2E-003 | |
| Dashboard workbench URL display | TC-UI-001, TC-UI-002, TC-UI-003, TC-UI-004, TC-E2E-001, TC-E2E-002, TC-E2E-003 | |
| HTTPRoute resource for unmigrated workbenches | TC-CFG-001, TC-URL-003, TC-E2E-002 | |
| Notebook Service port configuration | TC-CFG-002, TC-E2E-002 | |
| Workbench `inject-auth` annotation | TC-CFG-003, TC-CFG-004, TC-E2E-002, TC-E2E-003 | |
| Migrated workbench accessibility via Gateway | TC-URL-004, TC-UPG-002, TC-UPG-003, TC-E2E-002, TC-E2E-003 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-28 | Initial test plan |

---

**End of Test Plan**

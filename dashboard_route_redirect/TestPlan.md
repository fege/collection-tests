---
feature: dashboard_route_redirect
source_key: RHOAIENG-48787
source_type: issue
version: 1.0.0
status: In Review
author: RHOAI QE
additional_docs: []
last_updated: '2026-04-22'
reviewers: []
---
# Dashboard Route Redirect Test Plan
**RHOAI QE – Dashboard Upgrade Route Migration**

**Strategy**: [RHOAIENG-48787](https://redhat.atlassian.net/browse/RHOAIENG-48787)

---

## 1. Executive Summary

### 1.1 Purpose
This test plan validates that the RHOAI dashboard remains accessible after upgrading from pre-3.3 to 3.3, where the dashboard route changes from the `rhods-dashboard-redhat-ods-applications` pattern to `data-science-gateway`. The upgrade currently causes user disruption: users encounter loading errors and "Unauthorized" messages when attempting to access the dashboard via the previously known URL, and must manually discover the new route through the OpenShift console.

Testing must verify that users can access the dashboard seamlessly during and after the upgrade, either through automatic redirects from the old route to the new route, clear notifications about the route change, or maintained URL compatibility. This directly addresses a production-impacting issue where bookmarked URLs, SSO configurations, and external integrations break after the upgrade.

### 1.2 Scope

#### In Scope (RHOAI QE Responsibilities)
- Dashboard accessibility after upgrading from RHOAI pre-3.3 to 3.3
- Route migration from old pattern (`rhods-dashboard-redhat-ods-applications`) to new pattern (`data-science-gateway`)
- User access continuity after route migration (via whichever mechanism the implementation delivers — e.g., automatic redirect, user notification, or maintained URL compatibility)
- Dashboard authentication and authorization flow on the new route
- OpenShift console integration ("Red Hat OpenShift AI" link under "OpenShift Self Managed Services")

#### Out of Scope (Other Teams)
- Dashboard functionality unrelated to routing and accessibility
- RHOAI upgrades from 3.3 to later versions (forward upgrades)
- Non-dashboard components affected by the upgrade
- Initial installation (non-upgrade scenarios)
- Third-party integration certification beyond basic connectivity

### 1.3 Test Objectives
1. Verify that after upgrading to RHOAI 3.3, users can access the dashboard without encountering loading errors or "Unauthorized" messages at the old route URL (via whatever access-continuity mechanism the implementation delivers)
2. Verify that users attempting to access the old route during/after upgrade are guided to the new dashboard location (mechanism TBD — pending implementation design document)
3. Verify that dashboard authentication and authorization work correctly on the new `data-science-gateway` route after upgrade
4. Verify that bookmarked old route URLs do not result in "Unauthorized" or loading errors
5. Verify that the new dashboard route is accessible via the OpenShift console "Red Hat OpenShift AI" link

---

## 2. Test Strategy

### 2.1 Test Levels
- **Upgrade Testing** — Validate route behavior during RHOAI version upgrades, specifically pre-3.3 to 3.3
- **UI Testing** — Verify dashboard accessibility, error messages, and user experience when accessing old vs. new routes
- **Integration Testing** — Test route configuration, redirect mechanisms, and OpenShift route management
- **Functional Testing** — Validate user access continuity via the implemented mechanism (redirect, notification, or other) and route discovery workflows

### 2.2 Test Types
- **Positive Testing** — Verify new route works correctly after upgrade, user access continuity from old route is maintained via the implemented mechanism
- **Negative Testing** — Test old route access after upgrade without redirect, verify appropriate error handling when routes are unavailable
- **Regression Testing** — Ensure existing dashboard functionality remains intact after upgrade, verify new installations use correct route pattern
- **Boundary Testing** — Test multiple concurrent user sessions during route transition, bookmarked URLs vs. direct navigation

### 2.3 Test Priorities
- **P0 (Critical)** — Dashboard accessibility after upgrade; users must be able to access the dashboard via either automatic redirect or clear notification/guidance. Any failure blocks production use.
- **P1 (High)** — User experience during route transition; includes redirect timing, error message clarity, and notification visibility. Degraded UX affects user satisfaction but dashboard remains accessible.
- **P2 (Medium)** — Console link visibility, breadcrumb navigation, and secondary discoverability improvements for the new route.

---

## 3. Test Environment

### 3.1 Test Cluster Configuration
- OpenShift cluster (version TBD — pending release compatibility matrix in RHOAI 3.3 release manifest) with RHOAI pre-3.3 version installed (e.g., RHOAI 3.2.x — exact version TBD — pending release manifest)
- RHOAI 3.3 build (exact build/nightly identifier TBD — pending release manifest or Errata advisory) available for upgrade via OLM
- Dashboard operator and associated components operational
- Ingress/Route controller operational
- Minimum cluster resources: TBD — pending engineering sizing guide (CPU, memory, worker node count)
- Namespace configuration: `redhat-ods-applications` (pre-3.3); post-3.3 namespace TBD — pending RHOAI 3.3 installation documentation

### 3.2 Test Data Requirements
- Pre-3.3 dashboard route bookmark/URL (pattern: `rhods-dashboard-redhat-ods-applications`)
- Post-3.3 expected route (pattern: `data-science-gateway`)
- Valid authentication tokens/credentials for dashboard access
- TBD: Specific dashboard configuration files or ConfigMaps used in both versions — pending engineering design document for route migration

### 3.3 Test Users
- Authenticated user with permissions to access the RHOAI dashboard
- User with cluster-admin or project-admin role (for route inspection and upgrade execution)
- TBD: Service account requirements for automated testing — pending QE automation framework documentation
- TBD: Multiple user roles for permission verification scenarios — pending RBAC specification in RHOAI 3.3 product documentation

---

## 4. Routes/Endpoints Under Test

| Endpoint | Type | Purpose | Priority |
|----------|------|---------|----------|
| `rhods-dashboard-redhat-ods-applications/*` | OpenShift Route | Old dashboard route pattern (pre-3.3); verify users are not stranded with errors when accessing this URL after upgrade | P0 |
| `data-science-gateway/*` | OpenShift Route | New dashboard route (3.3+) that serves the dashboard application | P0 |
| OpenShift Console → "Red Hat OpenShift AI" link | UI Navigation | Navigation entry point under "OpenShift Self Managed Services" to discover new dashboard route | P1 |

---

## 5. Test Cases

Test cases have been generated. See the full index for details.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-UPG (Upgrade) | 3 | 2 P0, 1 P1 |
| TC-RDR (Redirect) | 3 | 1 P0, 2 P1 |
| TC-AUTH (Auth) | 3 | 2 P0, 1 P1 |
| TC-UI (UI/Navigation) | 3 | 1 P0, 1 P1, 1 P2 |
| TC-E2E (End-to-End) | 2 | 2 P0 |
| **Total** | **11** | **6 P0, 4 P1, 1 P2** |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- `TC-UPG` — Upgrade and migration test cases (route transition during upgrade)
- `TC-RDR` — Redirect and route resolution test cases (old route to new route behavior)
- `TC-AUTH` — Authentication and authorization test cases (dashboard access permissions)
- `TC-UI` — UI accessibility and navigation test cases (console links, dashboard loading)
- `TC-E2E` — End-to-end upgrade journey scenarios

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Full upgrade journey — pre-3.3 to 3.3 with route migration verification | `rhods-dashboard-*`, `data-science-gateway/*` | P0 |
| TC-E2E-002 | Bookmarked URL continuity journey after upgrade | `rhods-dashboard-*`, `data-science-gateway/*` | P0 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| `rhods-dashboard-redhat-ods-applications/*` | TC-E2E-001, TC-E2E-002 |
| `data-science-gateway/*` | TC-E2E-001, TC-E2E-002 |
| OpenShift Console → "Red Hat OpenShift AI" link | — (P1; E2E coverage via TC-UI-001) |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

**Not Applicable** — This issue concerns route management and URL redirection within an already-deployed OpenShift cluster. No external network dependencies, image pulls, or registry access are involved in the route transition mechanism.

### 7.2 Upgrade/Migration

**Critical NFR**:
- **Route migration behavior** — Test that old route (`rhods-dashboard-redhat-ods-applications`) is preserved or redirected during upgrade from pre-3.3 to 3.3+ (exact mechanism TBD — pending implementation design document)
- **Backwards compatibility** — Verify existing bookmarks and external links do not result in loading errors or "Unauthorized" messages after upgrade
- **Version-specific behavior** — Validate route patterns for the pre-3.3 to 3.3 upgrade path as described in the strategy
- **State preservation** — Verify user sessions, authentication tokens, and dashboard state survive route transition

### 7.3 Performance/Scalability

**Limited Applicability**:
- **Redirect latency** — Automatic redirects from old to new route should occur within acceptable time (<2s)
- **Concurrent access** — Multiple users accessing old route simultaneously during upgrade window should be handled gracefully
- **Route lookup performance** — OpenShift console route discovery should not degrade with large number of routes

If redirect mechanism is not implemented: **Not Applicable** — No redirect performance to measure; users manually navigate to new route.

### 7.4 RBAC/Authorization

**Moderate NFR**:
- **Route access permissions** — Verify same RBAC rules apply to new route as old route (no permission escalation or denial)
- **Authentication preservation** — Test that authentication state transfers across route change (no forced re-login)
- **Service account tokens** — If programmatic API access uses the route, verify tokens remain valid for new route
- **Unauthorized message accuracy** — Current "Unauthorized" error when accessing old route should be replaced with proper redirect or "route changed" message (not a permission error)

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| User lockout during upgrade window | High | High | Implement automatic redirect from old route to new route; test redirect in staging environment before production rollout |
| Bookmarked URLs and SSO configurations break | High | High | Preserve old route as alias or redirect; document route change in upgrade notes; provide migration scripts for SSO reconfiguration |
| Documentation and communication gaps | Medium | High | Add prominent upgrade notification to release notes; display in-product banner about route change; update all official documentation pre-release |
| Access-continuity mechanism not implemented or incomplete | Medium | Medium | Verify against acceptance criteria once defined; escalate gap to engineering if no mechanism exists after upgrade |
| OpenShift console link discovery is not obvious | Medium | High | Improve dashboard discoverability in OpenShift console; consider adding route change notification to console UI; provide direct link in upgrade completion message |
| Custom automation/scripts using old route URL fail | Medium | Medium | Provide backwards-compatible route alias; publish route change in API/integration documentation; notify integration partners before release |
| Third-party integrations (monitoring, logging) lose dashboard access | Medium | Low | Identify common integration points; test with representative third-party tools; provide migration guide for common integrations |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure
- Single OpenShift cluster capable of running RHOAI pre-3.3 and upgrading to 3.3
- Network access to cluster routes from external/test client
- Operator Lifecycle Manager (OLM) for RHOAI operator management
- `redhat-ods-applications` namespace (pre-3.3) and equivalent namespace structure in 3.3
- TBD: Multi-cluster setup requirements (if any) — pending QE infrastructure planning document
- TBD: Resource requirements (CPU, memory, storage) for test cluster — pending engineering sizing guide

### 9.2 Configuration
- RHOAI operator subscription configuration
- Dashboard deployment and service configurations
- Route resources for both old and new patterns
- TBD: Feature flags or configuration toggles for route migration behavior — pending engineering design document
- TBD: Environment variables controlling dashboard route naming — pending engineering design document
- TBD: Catalog source configuration for RHOAI operator versions — pending release manifest or Errata advisory

### 9.3 Test Tools
- `oc` CLI tool for OpenShift operations and route inspection
- `curl` or `httpie` for HTTP request testing and redirect verification
- Browser tools (Selenium, Playwright, or manual) for UI access testing
- `kubectl` for Kubernetes resource inspection
- Log viewing tools (`oc logs`, OpenShift console) for debugging route issues
- TBD: Automated upgrade testing framework — pending QE automation framework documentation
- TBD: Network traffic inspection tools for redirect analysis — pending QE tooling inventory

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-UPG | 3 | 2 | 1 | 0 |
| TC-RDR | 3 | 1 | 2 | 0 |
| TC-AUTH | 3 | 2 | 1 | 0 |
| TC-UI | 3 | 1 | 1 | 1 |
| TC-E2E | 2 | 2 | 0 | 0 |
| **Total** | **11** | **6** | **4** | **1** |

### 10.2 Route/Endpoint Coverage

| Endpoint | Test Cases | Coverage |
|----------|------------|----------|
| `rhods-dashboard-redhat-ods-applications/*` | TC-UPG-002, TC-RDR-001, TC-RDR-002, TC-RDR-003, TC-AUTH-002, TC-E2E-001, TC-E2E-002 | |
| `data-science-gateway/*` | TC-UPG-001, TC-UPG-003, TC-AUTH-001, TC-AUTH-003, TC-UI-002, TC-E2E-001, TC-E2E-002 | |
| OpenShift Console → "Red Hat OpenShift AI" link | TC-UI-001, TC-UI-003 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-22 | Initial test plan |
| 1.1.0 | 2026-04-22 | Added 11 test cases (6 P0, 4 P1, 1 P2) across 5 categories |

---

**End of Test Plan**

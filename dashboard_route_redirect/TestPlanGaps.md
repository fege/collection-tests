---
feature: dashboard_route_redirect
source_key: RHOAIENG-48787
status: Open
gap_count: 21
last_updated: '2026-04-22'
---
# Gaps — Dashboard Route Redirect

## Scope & Endpoints
- **Redirect implementation details** — HTTP status code, redirect type, duration of redirect support not specified. Would be resolved by: ADR / design doc
- **Notification mechanism specification** — Where/when/how users are informed about route changes is undefined. Would be resolved by: ADR / feature refinement
- **Authentication flow details** — Why users see "Unauthorized" on old route and whether auth configuration changed is unclear. Would be resolved by: API spec / design doc
- **Upgrade process integration** — When route migration occurs during upgrade and rollback strategy if upgrade fails is not documented. Would be resolved by: ADR / design doc
- **Backward compatibility policy** — How long old route redirects are maintained and version compatibility matrix is missing. Would be resolved by: ADR

## Test Strategy & Risks
- **No acceptance criteria defined** — The issue states acceptance criteria are not specified. Without defined criteria, it's unclear which solution approach is approved (automatic redirect vs. notification vs. seamless transition). Would be resolved by: feature refinement or product requirements document
- **No root cause analysis documented** — The issue describes symptoms but does not explain why the route changed (architectural decision, namespace consolidation, operator behavior change). Would be resolved by: ADR documenting the decision to change route patterns
- **No redirect implementation details** — If automatic redirect is chosen, there are no specifications for redirect mechanism, duration, or conditional logic. Would be resolved by: design doc
- **No notification mechanism specified** — If user notification is required, there are no details on channel, timing, or content. Would be resolved by: feature refinement
- **No multi-tenancy/namespace impact assessment** — Unclear if route change affects all tenants uniformly or if namespace-specific route patterns exist. Would be resolved by: design doc
- **No version compatibility matrix** — Missing information on which versions are affected, whether fresh 3.3 installs have the same issue, and forward compatibility. Would be resolved by: feature refinement

## Environment & Infrastructure
- **Exact RHOAI version requirements not specified** — Would be resolved by: feature refinement or design doc with version compatibility matrix
- **Upgrade procedure details missing** — Would be resolved by: ADR or design doc describing the upgrade process and route migration mechanism
- **Root cause of route change not documented** — Would be resolved by: ADR explaining architectural decision to change route naming convention
- **Redirect implementation approach unclear** — Would be resolved by: ADR or design doc specifying whether redirect should be automatic, notification-based, or both
- **Service account and RBAC requirements for automated testing** — Would be resolved by: ADR or feature refinement with security considerations
- **Specific namespace changes between versions** — Would be resolved by: design doc or feature refinement detailing namespace architecture changes
- **Test data artifacts (example YAML/JSON configurations)** — Would be resolved by: feature refinement with sample configurations for both versions
- **Performance and scale testing requirements** — Would be resolved by: feature refinement with non-functional requirements
- **Backward compatibility window** — Would be resolved by: ADR specifying how long old routes should remain functional or redirect
- **Multi-tenancy implications** — Would be resolved by: design doc addressing whether multiple dashboard instances are affected

## Test Case Coverage Gaps

No test case coverage gaps identified:
- All 3 endpoints from Section 4 have at least one test case
- Both P0 endpoints have E2E scenario coverage (TC-E2E-001, TC-E2E-002)
- All 5 test objectives from Section 1.3 are addressed by test cases
- P0 endpoints have P0 test cases assigned
- No test cases created for areas flagged as pending/missing in the gaps above

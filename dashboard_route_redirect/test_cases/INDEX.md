# Test Cases Index — Dashboard Route Redirect

**Test Plan**: [TestPlan.md](../TestPlan.md)

## Quick Stats

| Metric | Count |
|--------|-------|
| Total Test Cases | 11 |
| P0 (Critical) | 6 |
| P1 (High) | 4 |
| P2 (Medium) | 1 |

## TC-UPG — Upgrade and Migration

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-UPG-001](TC-UPG-001.md) | Dashboard accessible on new route after RHOAI upgrade to 3.3 | P0 |
| [TC-UPG-002](TC-UPG-002.md) | Old route does not strand users with errors after upgrade | P0 |
| [TC-UPG-003](TC-UPG-003.md) | OpenShift Route resource reflects new route pattern after upgrade | P1 |

## TC-RDR — Redirect and Route Resolution

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-RDR-001](TC-RDR-001.md) | Old route provides access continuity to new dashboard location | P0 |
| [TC-RDR-002](TC-RDR-002.md) | Old route subpaths resolve after upgrade | P1 |
| [TC-RDR-003](TC-RDR-003.md) | HTTP response from old route is appropriate after upgrade | P1 |

## TC-AUTH — Authentication and Authorization

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-AUTH-001](TC-AUTH-001.md) | Authentication works on new data-science-gateway route after upgrade | P0 |
| [TC-AUTH-002](TC-AUTH-002.md) | No misleading "Unauthorized" error on old route after upgrade | P0 |
| [TC-AUTH-003](TC-AUTH-003.md) | RBAC permissions consistent between old and new routes | P1 |

## TC-UI — UI Accessibility and Navigation

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-UI-001](TC-UI-001.md) | OpenShift console "Red Hat OpenShift AI" link navigates to new dashboard route | P1 |
| [TC-UI-002](TC-UI-002.md) | Dashboard renders correctly in browser on new route after upgrade | P0 |
| [TC-UI-003](TC-UI-003.md) | Console link visibility and placement under OpenShift Self Managed Services | P2 |

## TC-E2E — End-to-End Scenarios

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Full upgrade journey — pre-3.3 to 3.3 with route migration verification | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Bookmarked URL continuity journey after upgrade | P0 |

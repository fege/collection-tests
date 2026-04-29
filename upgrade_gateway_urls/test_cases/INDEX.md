# Test Cases Index — Upgrade Gateway URLs

**Test Plan**: [TestPlan.md](../TestPlan.md)
**Strategy**: [RHOAIENG-48747](https://redhat.atlassian.net/browse/RHOAIENG-48747)

## Summary

| Metric | Count |
|--------|-------|
| Total Test Cases | 18 |
| P0 (Critical) | 10 |
| P1 (High) | 7 |
| P2 (Medium) | 1 |

**Upgrade scenario**: Bug discovered during upgrade (RHOAI 2.25 to 3.3). All 18 TCs carry `upgrade_phase` tags (10 `both`, 8 `post`) — `both` TCs establish pre-upgrade PASS baselines for regression detection.

---

## URL Accessibility (TC-URL)

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-URL-001](TC-URL-001.md) | Legacy Route URL for Code-server workbench returns HTTP 200 | P0 |
| [TC-URL-002](TC-URL-002.md) | Legacy Route URL for JupyterLab workbench (with path) returns HTTP 200 | P0 |
| [TC-URL-003](TC-URL-003.md) | Gateway URL for unmigrated workbench does not return HTTP 500 | P0 |
| [TC-URL-004](TC-URL-004.md) | Migrated workbench accessible via Gateway URL | P1 |

## Dashboard Display (TC-UI)

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-UI-001](TC-UI-001.md) | Dashboard displays clickable workbench URLs for unmigrated workbenches | P0 |
| [TC-UI-002](TC-UI-002.md) | Dashboard displays Route-based URL format for unmigrated workbenches | P1 |
| [TC-UI-003](TC-UI-003.md) | Dashboard URL switches to Gateway URL after workbench migration | P1 |
| [TC-UI-004](TC-UI-004.md) | Dashboard handles stopped workbench URL display | P2 |

## K8s Configuration (TC-CFG)

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-CFG-001](TC-CFG-001.md) | HTTPRoute targets correct service port for unmigrated workbench | P0 |
| [TC-CFG-002](TC-CFG-002.md) | Base notebook Service exposes port 80 with targetPort 8888 | P0 |
| [TC-CFG-003](TC-CFG-003.md) | inject-auth annotation absent on unmigrated workbench | P1 |
| [TC-CFG-004](TC-CFG-004.md) | inject-auth annotation present after workbench migration | P1 |

## Upgrade Testing (TC-UPG)

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-UPG-001](TC-UPG-001.md) | All unmigrated workbench types accessible via legacy Routes after upgrade | P0 |
| [TC-UPG-002](TC-UPG-002.md) | Workbench migration to inject-auth produces working Gateway URLs | P1 |
| [TC-UPG-003](TC-UPG-003.md) | Mixed-state cluster handles migrated and unmigrated workbenches | P1 |

## End-to-End Scenarios (TC-E2E)

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Full upgrade journey — pre-upgrade baseline and post-upgrade workbench accessibility | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Migration journey — upgrade, migrate workbench, verify Gateway URLs | P0 |
| [TC-E2E-003](TC-E2E-003.md) | Mixed-state cluster — all workbench types and migration states | P0 |

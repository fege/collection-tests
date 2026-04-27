# Test Case Index — EvalHub Metrics Discovery

**Test Plan**: [TestPlan.md](../TestPlan.md)
**Strategy**: [RHAISTRAT-1507](https://redhat.atlassian.net/browse/RHAISTRAT-1507)

## Summary

| Metric | Count |
|--------|-------|
| Total Test Cases | 22 |
| P0 (Critical) | 9 |
| P1 (High) | 8 |
| P2 (Medium) | 5 |

## TC-SM — ServiceMonitor Lifecycle

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-SM-001](TC-SM-001.md) | ServiceMonitor auto-created when EvalHub deployed with metrics enabled | P0 |
| [TC-SM-002](TC-SM-002.md) | ServiceMonitor carries ownerReference to EvalHub CR | P0 |
| [TC-SM-003](TC-SM-003.md) | ServiceMonitor auto-deleted via Kubernetes GC when EvalHub CR removed | P0 |
| [TC-SM-004](TC-SM-004.md) | ServiceMonitor updated when EvalHub configuration changes | P1 |
| [TC-SM-005](TC-SM-005.md) | ServiceMonitor not created when prometheus.enabled is false | P1 |

## TC-SCRAPE — Prometheus Scraping

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-SCRAPE-001](TC-SCRAPE-001.md) | Prometheus discovers EvalHub as scrape target with state UP | P0 |
| [TC-SCRAPE-002](TC-SCRAPE-002.md) | Metrics queryable via Prometheus query API | P1 |
| [TC-SCRAPE-003](TC-SCRAPE-003.md) | Scrape interval is 30 seconds as configured | P2 |

## TC-NP — NetworkPolicy

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-NP-001](TC-NP-001.md) | NetworkPolicy permits Prometheus ingress to metrics port | P0 |
| [TC-NP-002](TC-NP-002.md) | Scraping succeeds in namespace with default-deny ingress | P0 |

## TC-RBAC — RBAC Permissions

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-RBAC-001](TC-RBAC-001.md) | Operator ClusterRole has ServiceMonitor CRUD permissions | P1 |
| [TC-RBAC-002](TC-RBAC-002.md) | Restricted user cannot modify operator-created ServiceMonitor | P1 |
| [TC-RBAC-003](TC-RBAC-003.md) | ServiceMonitor is namespace-scoped and isolated | P2 |

## TC-CFG — Configuration

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-CFG-001](TC-CFG-001.md) | ServiceMonitor TLS config has insecureSkipVerify enabled | P1 |
| [TC-CFG-002](TC-CFG-002.md) | ServiceMonitor targets correct port and path | P2 |

## TC-NEG — Negative Testing

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-NEG-001](TC-NEG-001.md) | Operator degrades gracefully when monitoring CRD absent | P1 |
| [TC-NEG-002](TC-NEG-002.md) | Prometheus scraping blocked when NetworkPolicy missing | P1 |
| [TC-NEG-003](TC-NEG-003.md) | ServiceMonitor creation fails when RBAC permissions incomplete | P1 |

## TC-UPGRADE — Upgrade/Migration

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-UPGRADE-001](TC-UPGRADE-001.md) | Existing EvalHub instances get ServiceMonitor after operator upgrade | P2 |
| [TC-UPGRADE-002](TC-UPGRADE-002.md) | ClusterRole RBAC updated during operator upgrade | P2 |
| [TC-UPGRADE-003](TC-UPGRADE-003.md) | No orphaned ServiceMonitor after operator rollback | P2 |

## TC-E2E — End-to-End Scenarios

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Deploy EvalHub → ServiceMonitor created → Prometheus scrapes → metrics queryable | P0 |
| [TC-E2E-002](TC-E2E-002.md) | NetworkPolicy and ServiceMonitor ship together for scraping in default-deny namespace | P0 |
| [TC-E2E-003](TC-E2E-003.md) | Deploy EvalHub → delete EvalHub → ServiceMonitor cleaned up | P0 |

---
feature: evalhub_metrics_discovery
source_key: RHAISTRAT-1507
status: Open
gap_count: 9
last_updated: '2026-04-27'
---
# Gaps — EvalHub Metrics Discovery

## Scope & Endpoints

- **Concrete API specification for ServiceMonitor resource fields** — The refinement doc specifies HTTPS port, `/metrics` path, 30s interval, `ownerReference`, and `insecureSkipVerify: true`, but exact YAML structure (e.g., `spec.endpoints[].port`, `spec.endpoints[].scheme`, `spec.endpoints[].tlsConfig`) is not provided. Would be resolved by: API spec or implementation PR with example YAML manifests.
- **NetworkPolicy exact ingress rule syntax** — The refinement doc states a NetworkPolicy is required for ingress from the monitoring namespace, but does not provide the exact rule specification (label selectors for Prometheus pods, port number). Would be resolved by: implementation PR or design doc with NetworkPolicy YAML.
- **Operator reconciliation loop method signatures** — Internal controller code structure (Go functions, reconciliation triggers) is not documented. Would be resolved by: implementation PR or design doc.
- **CRD presence detection implementation** — The refinement doc states the operator must "detect absence and degrade gracefully" but does not specify the degradation strategy (skip reconciliation? log-only? status condition update?). Would be resolved by: implementation PR or design doc with degradation state machine.

## Test Strategy & Risks

- **Alerting rule evaluation test procedure** — Acceptance Criterion #3 requires alerting rule evaluation but `PrometheusRule` definitions are explicitly out of scope. Test execution depends on manual `PrometheusRule` creation as a test fixture. Would be resolved by: example `PrometheusRule` YAML for test fixture.

## Environment & Infrastructure

- **OpenShift version requirement** — Exact OpenShift version compatible with RHOAI 3.5.EA1 is not specified. Would be resolved by: release documentation.
- **TrustyAI operator version** — Version with ServiceMonitor support is not identified. Would be resolved by: engineering team confirmation or build pipeline output.
- **EvalHub CR minimal valid spec** — Fields required to trigger ServiceMonitor creation are not fully enumerated. Would be resolved by: API spec or implementation PR.
- **Operator service account name** — Exact service account name for RBAC verification tests is not confirmed. Would be resolved by: implementation PR or deployment manifest review.

---
feature: upgrade_gateway_urls
source_key: RHOAIENG-48747
status: Open
gap_count: 15
last_updated: '2026-04-28'
---
# Gaps — Upgrade Gateway URLs

## Scope & Endpoints
- **Dashboard backend API specification** — the endpoint that fetches workbench URLs for UI display is not documented; would be resolved by an API spec or ADR
- **Controller service identification and interface** — the strategy mentions "3.x controller" behavior but does not identify which controller manages HTTPRoute creation or its API surface; would be resolved by an ADR or design doc
- **Workbench migration procedure and triggers** — how `inject-auth: true` is set (manual kubectl edit, automatic controller reconciliation, or API-driven) is not specified; would be resolved by a feature refinement or design doc
- **HTTPRoute and Service resource schemas** — exact YAML/JSON structure for HTTPRoutes and Service port mappings used in the 3.x authentication model are not provided; would be resolved by an ADR or API spec

## Test Strategy & Risks
- **Port mapping strategy for unmigrated workbenches** — whether the fix targets the HTTPRoute (change to port 80), the Service (expose port 8888), or the controller logic is not specified; would be resolved by an ADR or design doc
- **Migration trigger mechanism** — whether migration is automatic, manual, or admin-initiated is unclear; would be resolved by a feature refinement doc
- **Dashboard URL selection logic** — how the Dashboard decides to show a Route URL vs Gateway URL is not documented; would be resolved by a design doc
- **Service configuration per workbench type** — whether JupyterLab, Code-server, and RStudio have different service port configurations is not specified; would be resolved by an ADR or feature refinement
- **HTTPRoute reconciliation logic** — what triggers HTTPRoute creation/update for existing workbenches post-upgrade is not documented; would be resolved by an ADR or design doc

## Environment & Infrastructure
- **Exact workbench CR YAML structure for 2.x workbenches** — would be resolved by a feature refinement or design doc
- **Detailed HTTPRoute YAML created by 3.x controller** — would be resolved by an ADR or design doc
- **Service YAML showing port 80/targetPort 8888 configuration** — would be resolved by an ADR or design doc
- **Specific namespace and workbench naming conventions for test data** — would be resolved by a feature refinement
- **Expected HTTPRoute status fields beyond "Accepted"** — would be resolved by an ADR or API spec
- **Minimum OpenShift version required for Gateway API support** — would be resolved by a feature refinement or ADR

## Test Case Coverage Gaps

No coverage gaps identified:
- All 7 endpoints/resources from Section 4 have at least one test case
- All 5 P0 endpoints have P0 test cases
- All 5 P0 endpoints have E2E scenario coverage (TC-E2E-001, TC-E2E-002, TC-E2E-003)
- All 7 test objectives from Section 1.3 are addressed by at least one test case
- No test cases were created for endpoints or areas flagged as pending/missing in gaps above
- All 18 TCs carry `upgrade_phase` tags (10 `both`, 8 `post`) — 10 `both` TCs (incl. TC-E2E-001, TC-E2E-003, TC-UI-004) provide pre-upgrade PASS baselines for regression detection

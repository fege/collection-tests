# Test Case Index — Automated Upgrade Validation

**Source**: [RHAISTRAT-1519](https://redhat.atlassian.net/browse/RHAISTRAT-1519)
**Test Plan**: [TestPlan.md](../TestPlan.md)

## Quick Stats

- **Total Test Cases**: 33
- **P0 (Critical)**: 17
- **P1 (High)**: 12
- **P2 (Medium)**: 4

## Pipeline Trigger & Orchestration (TC-PIPE)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-PIPE-001](TC-PIPE-001.md) | Nightly release artifact triggers upgrade validation for all matrix paths | P0 |
| [TC-PIPE-002](TC-PIPE-002.md) | EA release artifact triggers upgrade validation | P0 |
| [TC-PIPE-003](TC-PIPE-003.md) | GA release artifact triggers upgrade validation | P0 |
| [TC-PIPE-004](TC-PIPE-004.md) | Pipeline rejects unsupported upgrade path | P1 |

## Cluster Deployment & Scenario Provisioning (TC-DEPLOY)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-DEPLOY-001](TC-DEPLOY-001.md) | Deploy cluster at pre-upgrade RHOAI version | P0 |
| [TC-DEPLOY-002](TC-DEPLOY-002.md) | Deploy component-owned scenarios as pre-upgrade baseline | P0 |
| [TC-DEPLOY-003](TC-DEPLOY-003.md) | Deploy cross-component scenarios as pre-upgrade baseline | P0 |
| [TC-DEPLOY-004](TC-DEPLOY-004.md) | Scenario deployment failure is reported with attribution | P1 |

## Pre-upgrade Checks & Prerequisite Resolution (TC-PRE)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-PRE-001](TC-PRE-001.md) | Pre-upgrade checks execute and report prerequisite status | P0 |
| [TC-PRE-002](TC-PRE-002.md) | Prerequisites are resolved successfully via odh-cli | P1 |
| [TC-PRE-003](TC-PRE-003.md) | Unresolvable prerequisite failure blocks upgrade execution | P0 |

## Platform Upgrade Execution (TC-UPG)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-UPG-001](TC-UPG-001.md) | Platform upgrade executes from pre-upgrade to target version | P0 |
| [TC-UPG-002](TC-UPG-002.md) | Upgrade completes within acceptable time budget | P2 |
| [TC-UPG-003](TC-UPG-003.md) | Operator convergence reached after upgrade | P0 |

## Post-upgrade Steps & Validation (TC-POST)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-POST-001](TC-POST-001.md) | Required post-upgrade steps execute successfully | P0 |
| [TC-POST-002](TC-POST-002.md) | Post-upgrade validation confirms workload continuity | P0 |
| [TC-POST-003](TC-POST-003.md) | Data-plane impact measured beyond control-plane health | P0 |

## Component-owned Scenario Validation (TC-COMP)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-COMP-001](TC-COMP-001.md) | Component workloads remain functional after upgrade | P0 |
| [TC-COMP-002](TC-COMP-002.md) | Component endpoints are accessible after upgrade | P0 |
| [TC-COMP-003](TC-COMP-003.md) | Component data persists correctly through upgrade | P1 |

## Cross-component Workflow Validation (TC-XCOMP)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-XCOMP-001](TC-XCOMP-001.md) | Cross-component workflow survives platform upgrade | P0 |
| [TC-XCOMP-002](TC-XCOMP-002.md) | Integration between components remains intact after upgrade | P0 |

## Result Publication & Failure Attribution (TC-RPT)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-RPT-001](TC-RPT-001.md) | Validation results are published automatically | P1 |
| [TC-RPT-002](TC-RPT-002.md) | Results are visible to release engineering and component teams | P1 |
| [TC-RPT-003](TC-RPT-003.md) | Failures are attributed to correct component or platform owner | P1 |
| [TC-RPT-004](TC-RPT-004.md) | Failure results include logs, tags, and reproduction steps | P2 |

## Release-readiness Gate Checks (TC-GATE)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-GATE-001](TC-GATE-001.md) | EA release blocked when upgrade validation fails | P0 |
| [TC-GATE-002](TC-GATE-002.md) | EA release proceeds when all upgrade validations pass | P0 |
| [TC-GATE-003](TC-GATE-003.md) | Bodies of Water transition requires passing upgrade validation | P1 |

## odh-cli Helper Validation (TC-CLI)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-CLI-001](TC-CLI-001.md) | odh-cli prerequisite resolution helper executes correctly | P1 |
| [TC-CLI-002](TC-CLI-002.md) | odh-cli post-upgrade action helper executes correctly | P1 |
| [TC-CLI-003](TC-CLI-003.md) | Upgrade completes successfully without odh-cli dependency | P1 |
| [TC-CLI-004](TC-CLI-004.md) | odh-cli helpers are documented for manual execution | P2 |

## Upgrade Matrix Extensibility (TC-MTX)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-MTX-001](TC-MTX-001.md) | New upgrade path added to matrix without pipeline changes | P1 |
| [TC-MTX-002](TC-MTX-002.md) | Upgrade matrix defines supported paths declaratively | P1 |

## End-to-End Upgrade Validation Scenarios (TC-E2E)

| Test Case | Title | Priority |
|-----------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Full upgrade validation cycle for supported upgrade path | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Disconnected mode upgrade validation cycle | P1 |
| [TC-E2E-003](TC-E2E-003.md) | Multi-path upgrade matrix validation with mixed results | P0 |

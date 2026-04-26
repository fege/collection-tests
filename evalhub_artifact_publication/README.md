# EvalHub Artifact Publication

Test plan for EvalHub's enhanced MLflow artifact publication workflow — surfacing artifact URIs in job status responses and signaling partial publication failures via `CompleteWithWarnings` status.

## Strategy

- **Jira**: [RHAISTRAT-1525](https://issues.redhat.com/browse/RHAISTRAT-1525)
- **Components**: Model Eval
- **Target versions**: eval-hub v0.3.0, eval-hub-sdk v0.1.5

## Artifacts

- [TestPlan.md](TestPlan.md) — Full test plan
- [TestPlanGaps.md](TestPlanGaps.md) — Identified gaps requiring additional documentation
- [TestPlanReview.md](TestPlanReview.md) — Quality review and scoring

## Test Cases

- [Test Case Index](test_cases/INDEX.md) — 26 test cases (17 P0, 6 P1, 3 P2)
- Categories: TC-API (4), TC-SDK (3), TC-STATUS (4), TC-SEC (2), TC-PERF (3), TC-COMPAT (3), TC-NEG (3), TC-E2E (4)

## Automated Tests

Automated test implementations will target:
- **eval-hub** repository — Go server API tests, PostgreSQL migration tests
- **eval-hub-sdk** repository — Python SDK DefaultCallbacks tests, unit tests with mock MLflow responses
- Integration tests with fault injection for partial failure scenarios

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-26 | Initial test plan generated from RHAISTRAT-1525 |
| 1.1.0 | 2026-04-26 | Updated with ADR: batch endpoint (POST /api/v1/evaluations/jobs/batch), audit logging, PostgreSQL 13+, Python 3.11+. Resolved 3 gaps. |

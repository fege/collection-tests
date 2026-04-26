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

- [Test Case Index](test_cases/INDEX.md) — 20 test cases (13 P0, 5 P1, 2 P2)
- Categories: TC-API (3), TC-SDK (3), TC-STATUS (4), TC-SEC (1), TC-PERF (2), TC-COMPAT (3), TC-NEG (2), TC-E2E (3)

## Automated Tests

Automated test implementations will target:
- **eval-hub** repository — Go server API tests, PostgreSQL migration tests
- **eval-hub-sdk** repository — Python SDK DefaultCallbacks tests, unit tests with mock MLflow responses
- Integration tests with fault injection for partial failure scenarios

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-26 | Initial test plan generated from RHAISTRAT-1525 |

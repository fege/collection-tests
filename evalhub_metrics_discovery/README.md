# EvalHub Metrics Discovery

Automatic integration of EvalHub metrics with the OpenShift platform monitoring stack via ServiceMonitor lifecycle management.

## References

- **Strategy**: [RHAISTRAT-1507](https://redhat.atlassian.net/browse/RHAISTRAT-1507)
- **Engineering Epic**: [RHOAIENG-58856](https://redhat.atlassian.net/browse/RHOAIENG-58856)
- **Implementation Task**: [RHOAIENG-58962](https://redhat.atlassian.net/browse/RHOAIENG-58962)

## Test Plan

- [TestPlan.md](TestPlan.md) — Full test plan document
- [TestPlanGaps.md](TestPlanGaps.md) — Identified gaps requiring additional documentation

## Test Cases

- [test_cases/INDEX.md](test_cases/INDEX.md) — Complete test case index
- **22 test cases**: 9 P0, 8 P1, 5 P2
- 8 categories: ServiceMonitor lifecycle, Prometheus scraping, NetworkPolicy, RBAC, Configuration, Negative testing, Upgrade/Migration, E2E

## Automated Tests

Automated test implementation will target the TrustyAI operator repository and/or the RHOAI downstream E2E test suite, depending on test case placement analysis.

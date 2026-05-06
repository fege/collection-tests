# Automated Upgrade Validation

Automated upgrade validation as a release quality gate for RHOAI — every release artifact executes the supported upgrade matrix and validates component-owned and cross-component scenarios.

## Links

- **Strategy**: [RHAISTRAT-1519](https://redhat.atlassian.net/browse/RHAISTRAT-1519)
- **Related**: [RHAISTRAT-1480 — Automated Migration: RHOAI 2.25 to 3.5 (EUS)](https://redhat.atlassian.net/browse/RHAISTRAT-1480)
- **Related**: [RHOAIENG-24681 — Openshift AI CI/CD : Release Process Reset](https://redhat.atlassian.net/browse/RHOAIENG-24681)

## Artifacts

- [TestPlan.md](TestPlan.md) — Full test plan
- [TestPlanGaps.md](TestPlanGaps.md) — Known gaps requiring additional documentation
- [test_cases/](test_cases/) — Individual test case specifications
- [test_cases/INDEX.md](test_cases/INDEX.md) — Test case index (33 TCs: 17 P0, 12 P1, 4 P2)

## Test Cases

**33 test cases** across 12 categories covering all upgrade pipeline steps.

| Priority | Count |
|----------|-------|
| P0 (Critical) | 17 |
| P1 (High) | 12 |
| P2 (Medium) | 4 |

See [test_cases/INDEX.md](test_cases/INDEX.md) for the full index.

## Test Implementation

Automated tests will be implemented in the RHOAI CI/CD pipeline infrastructure, targeting upgrade path validation across supported version matrices.

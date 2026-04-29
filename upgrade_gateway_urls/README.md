# Upgrade Gateway URLs

Validates that workbenches created in RHOAI 2.x remain accessible through the Dashboard after upgrading to RHOAI 3.x, addressing the broken Gateway URL issue (HTTP 500) caused by port mismatch in HTTPRoute configuration for unmigrated workbenches.

## Links

- **Jira**: [RHOAIENG-48747](https://redhat.atlassian.net/browse/RHOAIENG-48747)
- **Test Plan**: [TestPlan.md](TestPlan.md)
- **Gaps**: [TestPlanGaps.md](TestPlanGaps.md)

## Test Cases

**18 test cases** — [test_cases/INDEX.md](test_cases/INDEX.md)

| Priority | Count |
|----------|-------|
| P0 (Critical) | 10 |
| P1 (High) | 7 |
| P2 (Medium) | 1 |

## Test Automation

Automated tests will be implemented in the downstream E2E test repository, focusing on upgrade scenarios that validate workbench URL accessibility across the 2.x to 3.x upgrade boundary.

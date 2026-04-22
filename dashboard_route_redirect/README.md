# Dashboard Route Redirect

Test plan for validating dashboard accessibility after RHOAI 3.3 upgrade, where the route changes from `rhods-dashboard-redhat-ods-applications` to `data-science-gateway`.

## Links

- **Jira Issue**: [RHOAIENG-48787](https://redhat.atlassian.net/browse/RHOAIENG-48787)
- **Test Plan**: [TestPlan.md](TestPlan.md)

## Test Cases

- **Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)
- **Total**: 11 test cases (6 P0, 4 P1, 1 P2)
- **Categories**: Upgrade (3), Redirect (3), Auth (3), UI (3), E2E (2)

## Automated Tests

Test automation targets the RHOAI downstream E2E test suite, focusing on upgrade route migration verification using `oc` CLI and HTTP request validation.

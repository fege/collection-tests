# Hardware Profile Migration Bug Fix

Hardware profile migration from accelerator profile fails to add namespace annotation, causing webhook rejection and operator crashloop.

## Links

- **Jira**: [RHOAIENG-48676](https://issues.redhat.com/browse/RHOAIENG-48676)
- **Test Plan**: [TestPlan.md](TestPlan.md)
- **Fix PRs**:
  - [opendatahub-io/opendatahub-operator#3134](https://github.com/opendatahub-io/opendatahub-operator/pull/3134)
  - [red-hat-data-services/rhods-operator#17536](https://github.com/red-hat-data-services/rhods-operator/pull/17536)

## Test Cases

- **Index**: [test_cases/INDEX.md](test_cases/INDEX.md)
- **Total**: 18 test cases (8x P0, 6x P1, 4x P2)
- **Categories**: Migration Logic (5), Webhook Validation (4), Operator Stability (4), Regression (4), End-to-End (3)

## Automated Tests

- **Unit tests**: Go unit tests for migration logic (`upgrade_utils.go`) and webhook validation (`mutating.go`)
- **Cypress mocked tests**: UI-level migration flow validation

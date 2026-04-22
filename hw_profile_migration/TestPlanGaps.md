---
feature: hw_profile_migration
source_key: RHOAIENG-48676
status: Open
gap_count: 19
last_updated: '2026-04-21'
---
# Gaps -- Hardware Profile Migration

## Scope & Endpoints
- Exact function signatures and method names for the migration logic and webhook are not specified (only line references `upgrade_utils.go#L672` and `mutating.go#L237` provided)
- Specification of which Kubernetes API endpoints are used to read/write notebook annotations is implied but not explicit
- Definition of unit test coverage requirements (what functions/methods to test, edge cases) is not detailed
- Specification of Cypress test scenarios (what flows to mock, what assertions to make) is not specified
- Handling of edge cases: accelerator profiles in non-global namespaces, missing hardware profiles, orphaned annotations
- Validation criteria for "operator does not crashloop" (logs to check, metrics to monitor, reconciliation behavior)

## Test Strategy & Risks
- Missing webhook validation contract: the relationship between `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations is implicit in the code -- an API spec defining the annotation schema would prevent future discrepancies
- Undefined migration rollback/recovery strategy: no documented strategy for recovery, rollback, or manual intervention if migration fails (operator crashlooped for 22+ hours)
- Unclear hardware profile namespace hierarchy: no documented rule for when to use global vs. namespaced hardware profiles, or how the webhook resolves namespace lookups
- Missing Cypress mocked test coverage details: acceptance criteria mentions "Add/Update Cypress mocked tests" but doesn't specify what scenarios to mock
- No definition of "graceful" operator behavior during migration: expected behavior only states "operator should not crashloop," but doesn't define acceptable failure modes

## Environment & Infrastructure
- Accelerator Profile CR schema not documented
- Hardware Profile CR schema not documented
- Notebook CR schema not documented
- Exact migration trigger mechanism (operator version upgrade vs manual trigger) not specified
- Migration rollback or retry behavior (what happens after crashloop recovery) not defined
- Complete list of required RBAC permissions for operator service account not available
- Webhook configuration details (how it's registered, which operations it intercepts) not specified
- Container size migration scenario (the working code path at upgrade_utils.go#L672 that should be mirrored) not documented

## Test Case Coverage Gaps
- Test Objective 6 ("edge cases where accelerator profiles exist in non-global namespaces") has no dedicated test case — the strategy does not define expected behavior for this scenario, so no test case was created. Resolvable with an ADR or design doc clarifying namespace hierarchy rules.
- No Cypress-specific test cases generated — the acceptance criteria mention "Add/Update Cypress mocked tests" but the strategy does not specify what scenarios to mock or what assertions to make. Cypress test design requires framework-specific context not available from the strategy.

---
test_case_id: TC-CLI-004
source_key: RHAISTRAT-1519
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
upgrade_phase: both
---
# TC-CLI-004: odh-cli helpers are documented for manual execution

**Objective**: Verify that odh-cli helper commands are fully documented and that the documentation covers manual equivalent steps for customers who do not use the CLI.

**Preconditions**:
- odh-cli documentation is published

**Test Steps**:
1. Review odh-cli documentation for prerequisite resolution commands
2. Review odh-cli documentation for post-upgrade action commands
3. Verify each CLI command has a documented manual equivalent
4. Follow the manual equivalent steps on a test cluster to verify they work

**Expected Results**:
- Each odh-cli helper command is documented with usage, arguments, and expected output
- Each CLI command has a corresponding manual equivalent section
- The manual equivalent steps produce the same result as the CLI command

**Notes**: To be filled later in the process.

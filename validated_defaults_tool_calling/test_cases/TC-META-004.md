---
test_case_id: TC-META-004
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-META-004: Verify model card configuration contains a complete copy-paste-ready vllm serve command

**Objective**: Confirm that the "Tool Calling Configuration" section provides a full `vllm serve` command that a user can copy-paste without needing to assemble arguments manually.

**Test Steps**:
1. For each in-scope model, read the "Tool Calling Configuration" section from the model card
2. Locate the `vllm serve` command example in the section
3. Verify the command includes the model identifier and all four required flags in a single, copy-paste-ready line or block
4. Verify no instructions require the user to look up values elsewhere (e.g., "replace with your parser name")

**Expected Results**:
- Each model card contains a complete `vllm serve` command with all arguments pre-filled
- The command can be copied directly without modification (except optional environment-specific values like port or model path)
- No argument requires the user to consult external documentation to fill in

**Notes**: To be filled later in the process.

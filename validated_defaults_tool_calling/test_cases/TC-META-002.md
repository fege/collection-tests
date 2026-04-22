---
test_case_id: TC-META-002
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-META-002: Verify all four required CLI flags are present in model card configuration

**Objective**: Confirm that each model card's "Tool Calling Configuration" section includes all four required vLLM CLI arguments with non-empty values.

**Test Steps**:
1. For each in-scope model, read the "Tool Calling Configuration" section from the model card
2. Parse the section content and verify the presence of each required argument:
   - `--tool-call-parser` with a non-empty value
   - `--reasoning-parser` with a non-empty value (or explicitly marked as not required for this model)
   - `--chat-template` with a non-empty value (path or inline reference)
   - `--enable-auto-tool-choice` flag present
3. Verify that no argument has a placeholder value (e.g., "TBD", "TODO", "CHANGE_ME")

**Expected Results**:
- All four CLI arguments are present in every in-scope model's tool calling configuration
- Each argument has a concrete, non-placeholder value
- The `--tool-call-parser` value is a recognized parser name in the target vLLM version
- The `--chat-template` value references an existing template file or built-in name

**Notes**: To be filled later in the process.

---
test_case_id: TC-CFG-005
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-CFG-005: Verify documented vllm serve command can be copy-pasted and executed without modification

**Objective**: Confirm that the full `vllm serve` command from each model card can be copy-pasted into a terminal and starts vLLM successfully, reaching a ready state for inference.

**Preconditions**:
- Model weights are available at the path referenced in the model card
- GPU hardware meets the minimum requirements for the model (see Section 3.1 GPU table)
- Red Hat AI Inference Server version matches the version documented in the model card

**Test Steps**:
1. For each in-scope model, copy the complete `vllm serve` command from the model card's "Tool Calling Configuration" section
2. Paste and execute the command in a terminal with access to the model weights
3. Wait for vLLM to complete model loading and report ready status
4. Verify the server is accepting requests by querying `GET /v1/models`

**Expected Results**:
- The copy-pasted command starts vLLM without errors for each model
- No manual editing of the command is required (no missing values, no placeholder substitution)
- vLLM reaches ready state and responds to `/v1/models` with the served model listed
- All four tool calling flags are active as confirmed by startup logs

**Notes**: To be filled later in the process.

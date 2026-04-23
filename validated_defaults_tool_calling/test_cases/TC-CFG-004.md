---
test_case_id: TC-CFG-004
source_key: RHAISTRAT-1473
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-CFG-004: Validate requiredArgs contains valid CLI arguments

**Objective**: Confirm that any `requiredArgs` entries in `servingConfig.toolCalling` are valid vLLM CLI arguments that can be passed without causing startup failures.

**Preconditions**:
- At least one in-scope model has `requiredArgs` populated in its `servingConfig.toolCalling` (e.g., `["--config_format granite"]`)

**Test Steps**:
1. Identify a model with `requiredArgs` in its `servingConfig.toolCalling`
2. Extract the `requiredArgs` array values
3. Start vLLM with all standard flags plus the `requiredArgs` values:
   ```bash
   vllm serve <model-path> \
     --tool-call-parser=<toolCallParser> \
     --chat-template=<chatTemplate> \
     --enable-auto-tool-choice \
     <requiredArgs values>
   ```
4. Check vLLM startup logs for any argument parsing errors
5. Verify vLLM reaches ready state

**Expected Results**:
- vLLM starts successfully with all `requiredArgs` values included
- No `unrecognized arguments` or `invalid value` errors appear in logs
- The model loads and reaches ready state as verified via `GET /v1/models`

**Notes**: To be filled later in the process.

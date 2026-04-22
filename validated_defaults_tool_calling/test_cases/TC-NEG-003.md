---
test_case_id: TC-NEG-003
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-NEG-003: Verify serving a model with missing --chat-template produces a detectable failure

**Objective**: Confirm that omitting the `--chat-template` argument (when the model requires one for tool calling) results in a detectable failure.

**Preconditions**:
- One in-scope model is available on target hardware
- The model requires a specific chat template for tool calling (as documented in the model card)

**Test Steps**:
1. Start vLLM with the correct `--tool-call-parser` and `--enable-auto-tool-choice` but WITHOUT `--chat-template`:
   ```bash
   vllm serve <model-path> --tool-call-parser=<correct-value> --enable-auto-tool-choice
   ```
2. If vLLM starts, send a tool calling request with a simple tool definition
3. Examine the response and logs for template-related failures:
   - Tool definitions not injected into the prompt
   - `tool_calls` array is empty despite a clear tool-requiring prompt
   - Template rendering errors in server logs

**Expected Results**:
- vLLM either fails to start with a clear error about the missing chat template OR
- Tool calling requests fail or produce incorrect results without the template
- The failure is detectable and not silent

**Notes**: To be filled later in the process.

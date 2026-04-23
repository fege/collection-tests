---
test_case_id: TC-NEG-003
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-003: Missing chatTemplate produces detectable failure

**Objective**: Confirm that deploying a model without specifying the `chatTemplate` (or with a non-existent path) results in a detectable failure.

**Preconditions**:
- One in-scope model is available on target hardware
- The correct `chatTemplate` path is known from `servingConfig.toolCalling`

**Test Steps**:
1. Select an in-scope model that requires a custom `chatTemplate`
2. Deploy the model without the `--chat-template` flag:
   ```bash
   vllm serve <model-path> \
     --tool-call-parser=<correct-toolCallParser> \
     --enable-auto-tool-choice
   ```
3. Alternatively, deploy with a non-existent template path:
   ```bash
   vllm serve <model-path> \
     --tool-call-parser=<correct-toolCallParser> \
     --chat-template=/nonexistent/path/template.jinja \
     --enable-auto-tool-choice
   ```
4. Check vLLM startup logs for errors
5. If vLLM starts, send a tool calling request and check for malformed responses

**Expected Results**:
- Either vLLM fails to start with a `FileNotFoundError` or template loading error, OR
- Tool calling responses are incorrect (wrong format, no `tool_calls`, parsing failures)
- The failure is detectable and clearly attributable to the missing/incorrect chat template

**Notes**: To be filled later in the process.

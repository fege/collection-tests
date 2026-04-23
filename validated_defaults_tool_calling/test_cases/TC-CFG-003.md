---
test_case_id: TC-CFG-003
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-CFG-003: Validate enableAutoToolChoice is set and functional

**Objective**: Confirm that when `enableAutoToolChoice` is `true` in `servingConfig.toolCalling`, vLLM correctly auto-selects tools without requiring `tool_choice: required` in the request.

**Test Steps**:
1. Select one in-scope model with `enableAutoToolChoice: true` in its `servingConfig.toolCalling`
2. Start vLLM with `--enable-auto-tool-choice` along with the model's `toolCallParser` and `chatTemplate`:
   ```bash
   vllm serve <model-path> --tool-call-parser=<value> --chat-template=<value> --enable-auto-tool-choice
   ```
3. Wait for vLLM to reach ready state (verify via `GET /v1/models`)
4. Send a tool calling request with `tool_choice: "auto"` (NOT `"required"`):
   ```json
   {
     "model": "<model-name>",
     "messages": [{"role": "user", "content": "What is the weather in Boston today?"}],
     "tools": [{"type": "function", "function": {"name": "get_weather", "description": "Get the current weather for a location", "parameters": {"type": "object", "properties": {"location": {"type": "string"}}, "required": ["location"]}}}],
     "tool_choice": "auto"
   }
   ```
5. Verify the response contains a `tool_calls` array with the model auto-selecting `get_weather`

**Expected Results**:
- vLLM starts successfully with `--enable-auto-tool-choice`
- The model auto-selects the appropriate tool without `tool_choice: required`
- Response contains a valid `tool_calls` array with `function.name` = `get_weather`
- `finish_reason` is `"tool_calls"`

**Notes**: To be filled later in the process.

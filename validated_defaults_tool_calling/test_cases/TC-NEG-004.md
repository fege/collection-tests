---
test_case_id: TC-NEG-004
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-004: Without --enable-auto-tool-choice, model does not auto-select tools

**Objective**: Confirm that omitting `--enable-auto-tool-choice` prevents vLLM from automatically selecting tools, even when a valid `toolCallParser` and `chatTemplate` are configured.

**Preconditions**:
- One in-scope model is available on target hardware

**Test Steps**:
1. Start vLLM with correct `toolCallParser` and `chatTemplate` but WITHOUT `--enable-auto-tool-choice`:
   ```bash
   vllm serve <model-path> \
     --tool-call-parser=<correct-toolCallParser> \
     --chat-template=<correct-chatTemplate>
   ```
2. Send a tool calling request with `"tool_choice": "auto"` and a prompt that clearly requires a tool:
   ```json
   {
     "model": "<model-name>",
     "messages": [{"role": "user", "content": "What is the weather in Boston today?"}],
     "tools": [{"type": "function", "function": {"name": "get_weather", "description": "Get the current weather for a location", "parameters": {"type": "object", "properties": {"location": {"type": "string"}}, "required": ["location"]}}}],
     "tool_choice": "auto"
   }
   ```
3. Examine the response for tool calling behavior

**Expected Results**:
- Without `--enable-auto-tool-choice`, the model does NOT automatically invoke tools
- The response either contains a text-only reply (no `tool_calls` array) or the behavior differs from when the flag is enabled
- This confirms the flag is required for the validated configuration to work as documented

**Notes**: To be filled later in the process.

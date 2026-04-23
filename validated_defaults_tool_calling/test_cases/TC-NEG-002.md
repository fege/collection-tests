---
test_case_id: TC-NEG-002
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-NEG-002: Wrong toolCallParser produces detectable failure

**Objective**: Confirm that deploying a model with an incorrect `toolCallParser` value results in a detectable failure — either at vLLM startup or in the tool calling response.

**Preconditions**:
- One in-scope model is available on target hardware
- The correct `toolCallParser` value is known from `servingConfig.toolCalling`

**Test Steps**:
1. Select an in-scope model and identify its correct `toolCallParser` value
2. Deploy the model with an intentionally wrong parser value:
   ```bash
   vllm serve <model-path> \
     --tool-call-parser=wrong_parser_value \
     --chat-template=<correct-chatTemplate> \
     --enable-auto-tool-choice
   ```
3. If vLLM starts, send a tool calling request:
   ```json
   {
     "model": "<model-name>",
     "messages": [{"role": "user", "content": "What is the weather in Boston today?"}],
     "tools": [{"type": "function", "function": {"name": "get_weather", "description": "Get the current weather for a location", "parameters": {"type": "object", "properties": {"location": {"type": "string"}}, "required": ["location"]}}}],
     "tool_choice": "auto"
   }
   ```
4. Examine the startup logs or the response for errors or malformed output

**Expected Results**:
- Either vLLM fails to start with a clear error about the invalid parser, OR
- The tool calling response is malformed (e.g., empty `tool_calls`, unparseable `arguments`, or an error response)
- The failure is detectable and distinguishable from a successful tool calling interaction

**Notes**: To be filled later in the process.

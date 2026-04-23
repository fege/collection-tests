---
test_case_id: TC-CFG-005
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-CFG-005: Verify complete vllm serve command from servingConfig fields can be executed without modification

**Objective**: Confirm that a complete `vllm serve` command constructed from `servingConfig.toolCalling` fields (`toolCallParser`, `chatTemplate`, `enableAutoToolChoice`, `requiredArgs`) can be executed verbatim and the model successfully loads and accepts tool calling requests.

**Test Steps**:
1. For one in-scope model, extract all `servingConfig.toolCalling` fields from the CatalogModel
2. Construct the full `vllm serve` command:
   ```bash
   vllm serve <model-path> \
     --tool-call-parser=<toolCallParser> \
     --chat-template=<chatTemplate> \
     --enable-auto-tool-choice \
     <requiredArgs values if present>
   ```
3. Execute the command without any modifications
4. Wait for vLLM to reach ready state (verify via `GET /v1/models`)
5. Send a tool calling request:
   ```json
   {
     "model": "<model-name>",
     "messages": [{"role": "user", "content": "What is the weather in Boston today?"}],
     "tools": [{"type": "function", "function": {"name": "get_weather", "description": "Get the current weather for a location", "parameters": {"type": "object", "properties": {"location": {"type": "string"}}, "required": ["location"]}}}],
     "tool_choice": "auto"
   }
   ```
6. Verify the model responds with a valid `tool_calls` array

**Expected Results**:
- The constructed `vllm serve` command executes successfully on first attempt
- vLLM reaches ready state without errors
- Tool calling request returns a valid response with `tool_calls` and `finish_reason: "tool_calls"`
- No manual adjustments or undocumented flags are needed

**Notes**: To be filled later in the process.

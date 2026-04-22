---
test_case_id: TC-CFG-004
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Complete
last_updated: '2026-04-22'
automation_file: tests/model_registry/model_catalog/metadata/test_tool_calling_config.py
automation_function: test_enable_auto_tool_choice_in_model_card
---
# TC-CFG-004: Validate --enable-auto-tool-choice flag is set and functional for each in-scope model

**Objective**: Confirm that the `--enable-auto-tool-choice` flag is documented in each model card and that enabling it allows vLLM to automatically select tools based on user prompts.

**Test Steps**:
1. For each in-scope model, verify the model card's "Tool Calling Configuration" section includes `--enable-auto-tool-choice`
2. Start vLLM with all four documented flags, including `--enable-auto-tool-choice`
3. Send a tool calling request with `"tool_choice": "auto"` and a prompt that clearly requires a tool (e.g., weather query with a weather tool defined)
4. Verify that vLLM automatically selects the appropriate tool without the user explicitly specifying `"tool_choice": {"type": "function", "function": {"name": "..."}}`

**Test Data**:
```json
{
  "model": "<model-name>",
  "messages": [{"role": "user", "content": "What is the weather in Boston today?"}],
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "get_weather",
        "description": "Get the current weather for a location",
        "parameters": {"type": "object", "properties": {"location": {"type": "string"}}, "required": ["location"]}
      }
    }
  ],
  "tool_choice": "auto"
}
```

**Expected Results**:
- vLLM responds with a `tool_calls` array containing the selected tool
- The `function.name` in the response matches `get_weather`
- `finish_reason` is `"tool_calls"`

**Notes**: To be filled later in the process.

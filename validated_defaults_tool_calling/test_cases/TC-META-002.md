---
test_case_id: TC-META-002
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-002: Verify servingConfig.toolCalling present in API response for validated model

**Objective**: Confirm that `GET /api/catalog/models/{modelId}` returns a typed `servingConfig.toolCalling` block when the model has a validated tool calling configuration.

**Preconditions**:
- AI Hub catalog service deployed with `ServingConfig`/`ToolCallingConfig` schema additions
- At least one model loaded from `metadata.yaml` with `servingConfig.toolCalling` populated

**Test Steps**:
1. Retrieve a model with validated tool calling config: `GET /api/catalog/models/{modelId}`
2. Parse the JSON response and locate the `servingConfig` field
3. Verify `servingConfig.toolCalling.supported` is `true`
4. Verify `servingConfig.toolCalling.toolCallParser` is a non-empty string
5. Verify `servingConfig.toolCalling.chatTemplate` is a non-empty string
6. Verify `servingConfig.toolCalling.enableAutoToolChoice` is a boolean
7. If `requiredArgs` is present, verify it is an array of strings

**Expected Results**:
- `servingConfig` field is present in the response
- `toolCalling` sub-object contains all expected typed fields
- Field values match what was loaded from `metadata.yaml`

**Expected Response**:
```json
{
  "name": "Granite-4.0-H-Small",
  "tasks": ["text-generation", "tool-calling"],
  "servingConfig": {
    "toolCalling": {
      "supported": true,
      "toolCallParser": "granite",
      "chatTemplate": "opt/app-root/template/tool_chat_template_granite.jinja",
      "enableAutoToolChoice": true,
      "requiredArgs": ["--config_format granite"]
    }
  }
}
```

**Notes**: To be filled later in the process.

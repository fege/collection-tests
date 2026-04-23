---
test_case_id: TC-META-005
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-005: Verify mapDBModelToAPIModel() deserializes serving_config to typed ServingConfig

**Objective**: Confirm that `mapDBModelToAPIModel()` in `db_catalog.go` correctly deserializes the `serving_config` JSON property from the database into a typed `ServingConfig` object in the API model.

**Preconditions**:
- Go test environment with access to `db_catalog.go` source
- Database with `serving_config` property rows containing valid JSON

**Test Steps**:
1. Insert a `serving_config` property row with valid JSON: `{"toolCalling":{"supported":true,"toolCallParser":"granite","chatTemplate":"opt/app-root/template/tool_chat_template_granite.jinja","enableAutoToolChoice":true,"requiredArgs":["--config_format granite"]}}`
2. Call `mapDBModelToAPIModel()` for this model
3. Verify the returned model has `ServingConfig` populated (not nil)
4. Verify `ServingConfig.ToolCalling.Supported` is `true`
5. Verify `ServingConfig.ToolCalling.ToolCallParser` is `"granite"`
6. Verify `ServingConfig.ToolCalling.ChatTemplate` is `"opt/app-root/template/tool_chat_template_granite.jinja"`
7. Verify `ServingConfig.ToolCalling.EnableAutoToolChoice` is `true`
8. Verify `ServingConfig.ToolCalling.RequiredArgs` is `["--config_format granite"]`
9. Repeat with a model that has no `serving_config` property — verify `ServingConfig` is nil

**Expected Results**:
- All `ToolCallingConfig` fields are correctly deserialized from JSON to typed Go struct
- Models without `serving_config` property have `ServingConfig` as nil (field omitted from API response)

**Notes**: To be filled later in the process.

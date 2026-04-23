---
test_case_id: TC-META-004
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-004: Verify convertModelProperties() serializes servingConfig to serving_config JSON property

**Objective**: Confirm that `convertModelProperties()` in `yaml_catalog.go` correctly serializes the `servingConfig` object to a `serving_config` JSON string property in the database Properties table.

**Preconditions**:
- Go test environment with access to `yaml_catalog.go` source
- Test fixtures with YAML model entries containing `servingConfig.toolCalling`

**Test Steps**:
1. Create a YAML model entry with a complete `servingConfig.toolCalling` block
2. Call `convertModelProperties()` with this model
3. Inspect the returned properties list for a `serving_config` entry
4. Verify `serving_config` property has `IsCustomProperty = false`
5. Verify the property value is valid JSON
6. Parse the JSON and verify it contains all `toolCalling` fields with correct values
7. Repeat with a model that has NO `servingConfig` — verify no `serving_config` property is emitted

**Expected Results**:
- `serving_config` property is emitted with valid JSON containing all `toolCalling` fields
- JSON field names use camelCase (matching OpenAPI schema)
- Models without `servingConfig` produce no `serving_config` property row

**Validation**:
- Query Properties table: `SELECT value FROM Properties WHERE property_name = 'serving_config' AND is_custom_property = false`
- Verify JSON parses correctly: `echo "$value" | jq .toolCalling.supported` returns `true`

**Notes**: To be filled later in the process.

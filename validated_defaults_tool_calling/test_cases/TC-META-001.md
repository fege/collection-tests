---
test_case_id: TC-META-001
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-001: Verify ToolCallingConfig OpenAPI schema defines correct fields, types, and constraints

**Objective**: Confirm that `ServingConfig` and `ToolCallingConfig` schemas in `api/openapi/src/catalog.yaml` correctly define required/optional fields, types, defaults, and examples.

**Test Steps**:
1. Open `api/openapi/src/catalog.yaml` and locate `ServingConfig` schema
2. Verify `ServingConfig` has `toolCalling` property referencing `ToolCallingConfig`
3. Verify `ToolCallingConfig` has `required: [supported]`
4. Verify `ToolCallingConfig.supported` is `type: boolean`
5. Verify `ToolCallingConfig.toolCallParser` is `type: string` with `example: granite`
6. Verify `ToolCallingConfig.chatTemplate` is `type: string` with `example: opt/app-root/template/tool_chat_template_granite.jinja`
7. Verify `ToolCallingConfig.enableAutoToolChoice` is `type: boolean` with `default: true`
8. Verify `ToolCallingConfig.requiredArgs` is `type: array` with `items: type: string`
9. Verify `CatalogModel` properties include `servingConfig` referencing `ServingConfig`
10. Regenerate Go types via `make -C catalog gen/openapi` and `make gen/openapi-server` — verify no errors

**Expected Results**:
- All field types, required constraints, defaults, and examples match the ADR specification
- Code generation succeeds without errors
- `servingConfig` is an optional property on `CatalogModel` (not on `BaseModel`)

**Notes**: To be filled later in the process.

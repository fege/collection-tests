---
test_case_id: TC-META-006
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-006: Verify model-metadata-collection pipeline outputs servingConfig and removes Markdown

**Objective**: Confirm that the model-metadata-collection pipeline outputs `servingConfig.toolCalling` in `metadata.yaml` from HuggingFace YAML frontmatter and removes the tool-calling Markdown section from README rendering.

**Preconditions**:
- Access to model-metadata-collection pipeline
- HuggingFace model repo with tool-calling configuration in YAML frontmatter

**Test Steps**:
1. Run the model-metadata-collection pipeline for a model with tool-calling configuration (e.g., Granite-4.0-H-Small)
2. Open the generated `metadata.yaml` and locate the model entry
3. Verify `servingConfig.toolCalling` block is present with `supported`, `toolCallParser`, `chatTemplate`, `enableAutoToolChoice` fields
4. Verify the `tasks` array includes `tool-calling`
5. Open the generated model README
6. Verify the `## vLLM Deployment with Tool Calling` Markdown section is NOT present (removed by pipeline)
7. Repeat for a model WITHOUT tool-calling configuration — verify `servingConfig` is absent

**Expected Results**:
- `servingConfig.toolCalling` is present in `metadata.yaml` for models with validated configs
- `RenderToolCallingSection()` template call is not invoked — no Markdown section in README
- Models without configs omit `servingConfig` entirely

**Notes**: To be filled later in the process.

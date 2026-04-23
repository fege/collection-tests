---
test_case_id: TC-PROC-001
source_key: RHAISTRAT-1473
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-PROC-001: Documented process exists for adding new tool calling configs to model-metadata-collection

**Objective**: Confirm that the model-metadata-collection repository includes a documented process for adding validated tool calling configurations (specifically `servingConfig.toolCalling` entries) for new models.

**Test Steps**:
1. Access the model-metadata-collection repository
2. Search for documentation covering the process of adding a new model's tool calling configuration (e.g., CONTRIBUTING.md, README.md, or a dedicated process doc)
3. Verify the documentation covers:
   - Where to add the `servingConfig.toolCalling` configuration data (which file, which schema fields)
   - What values are required (`toolCallParser`, `chatTemplate`, `enableAutoToolChoice`) and optional (`requiredArgs`)
   - What validation steps are required before merging (e.g., vLLM startup test, tool calling round-trip test)
   - Who reviews and approves new configurations
4. Verify the documentation is accessible from the repository root (linked from README or discoverable)

**Expected Results**:
- A documented process exists for adding validated tool calling configurations for new models
- The process covers location, format, required fields, validation requirements, and approval workflow
- The documentation references the `servingConfig.toolCalling` schema structure
- The documentation is discoverable from the repository root

**Notes**: To be filled later in the process.

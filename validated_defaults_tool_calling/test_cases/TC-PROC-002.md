---
test_case_id: TC-PROC-002
source_key: RHAISTRAT-1473
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-PROC-002: Follow documented process to add a new model's tool calling config end-to-end

**Objective**: Validate that the documented process for adding a new model's `servingConfig.toolCalling` configuration is accurate, complete, and can be followed by an engineer unfamiliar with the repository.

**Preconditions**:
- Access to a fork or test branch of the model-metadata-collection repository
- A test model with known valid tool calling configuration values (`toolCallParser`, `chatTemplate`, `enableAutoToolChoice`, optionally `requiredArgs`)

**Test Steps**:
1. Read the documented process for adding new validated tool calling configurations (verified in TC-PROC-001)
2. Follow the process step-by-step to add a `servingConfig.toolCalling` configuration for a test model
3. Verify that each step in the documentation is accurate (correct file paths, correct schema structure, no missing steps)
4. Submit the configuration through the documented review/approval workflow
5. Verify the configuration appears correctly in the catalog API response after merging:
   ```
   GET /api/v1/models/<test-model-id>
   ```
6. Confirm `servingConfig.toolCalling` contains the submitted values

**Expected Results**:
- The documented process can be followed end-to-end without needing to consult additional resources
- Each step in the documentation accurately describes the current repository structure and workflow
- The resulting catalog API response correctly includes `servingConfig.toolCalling` with the submitted values
- The new model can be deployed and used for tool calling using the published configuration

**Notes**: To be filled later in the process.

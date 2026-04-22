---
test_case_id: TC-PROC-002
source_key: RHAISTRAT-1473
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-PROC-002: Follow the documented process to add a new model's tool calling config

**Objective**: Validate that the documented process for adding a new model's tool calling configuration is accurate, complete, and can be followed by an engineer unfamiliar with the repository.

**Preconditions**:
- Access to a fork or test branch of the model-metadata-collection repository
- A test model with known valid tool calling configuration values

**Test Steps**:
1. Read the documented process for adding new validated tool calling configurations (verified in TC-PROC-001)
2. Follow the process step-by-step to add a tool calling configuration for a test model
3. Verify that each step in the documentation is accurate (correct file paths, correct format, no missing steps)
4. Submit the configuration through the documented review/approval workflow
5. Verify the configuration appears correctly in the model card after merging

**Expected Results**:
- The documented process can be followed end-to-end without needing to consult additional resources
- Each step in the documentation accurately describes the current repository structure and workflow
- The resulting model card correctly displays the "Tool Calling Configuration" section with the submitted values

**Notes**: To be filled later in the process.

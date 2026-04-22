---
test_case_id: TC-META-003
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-META-003: Verify model card configuration is tied to a specific inference server version

**Objective**: Confirm that each published tool calling configuration in the model card specifies the Red Hat AI Inference Server version it was validated against.

**Test Steps**:
1. For each in-scope model, read the "Tool Calling Configuration" section from the model card
2. Verify that the section includes a version reference for the Red Hat AI Inference Server (vLLM) against which the configuration was validated
3. Confirm the referenced version matches a released or candidate version of the inference server

**Expected Results**:
- Each model card's tool calling configuration includes the inference server version it was validated against
- The version string is concrete (e.g., "0.8.4" or "RHOAI 3.5 GA"), not vague (e.g., "latest")
- The version matches the inference server version shipped in the target RHOAI release

**Notes**: To be filled later in the process.

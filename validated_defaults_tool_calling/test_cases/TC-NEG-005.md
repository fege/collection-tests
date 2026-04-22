---
test_case_id: TC-NEG-005
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-NEG-005: Verify model card with partial configuration is not published

**Objective**: Confirm that the model-metadata-collection publishing process rejects model cards with incomplete tool calling configurations (partial data), preventing unvalidated content from reaching users.

**Test Steps**:
1. Create a test model card with a "Tool Calling Configuration" section that has only some of the four required arguments (e.g., `--tool-call-parser` present but `--chat-template` missing)
2. Attempt to publish or merge this model card into the model-metadata-collection repository following the documented process
3. Verify that the publishing process catches the incomplete configuration

**Expected Results**:
- The publishing process rejects or flags the model card with incomplete tool calling configuration
- The partial configuration does NOT reach the published model catalog
- An error or warning message identifies which required arguments are missing

**Notes**: To be filled later in the process.

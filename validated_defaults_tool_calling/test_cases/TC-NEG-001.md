---
test_case_id: TC-NEG-001
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-NEG-001: Verify model card without validated config does not display "Tool Calling Configuration" section

**Objective**: Confirm that model cards for models that have NOT been validated for tool calling do not contain a "Tool Calling Configuration" section, ensuring users are never presented with partial or unvalidated data.

**Test Steps**:
1. Identify at least one model in the model-metadata-collection repository that is NOT in the initial validated scope
2. Read the model card for that model
3. Search the model card content for any mention of "Tool Calling Configuration"
4. Verify the section is completely absent (not present as an empty section, not hidden, not commented out)

**Expected Results**:
- Models without validated tool calling configurations have no "Tool Calling Configuration" section in their model card
- No partial configurations, placeholder sections, or "coming soon" notes appear for unvalidated models
- The model card is otherwise complete and functional for its non-tool-calling features

**Notes**: To be filled later in the process.

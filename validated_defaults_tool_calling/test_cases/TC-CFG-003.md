---
test_case_id: TC-CFG-003
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-CFG-003: Validate --chat-template value loads correctly for each in-scope model

**Objective**: Confirm that the `--chat-template` value documented in each model card references a valid Jinja template that loads without errors.

**Test Steps**:
1. For each in-scope model, extract the `--chat-template` value from the model card's "Tool Calling Configuration" section
2. Start vLLM with the documented chat template:
   ```bash
   vllm serve <model-path> --chat-template=<documented-value> --tool-call-parser=<documented-value> --enable-auto-tool-choice
   ```
3. Check vLLM startup logs for template loading confirmation
4. Verify that no Jinja template errors (e.g., `TemplateNotFound`, `TemplateSyntaxError`) appear in logs

**Expected Results**:
- vLLM successfully loads the documented chat template for each model
- No template-related errors appear in startup or request-handling logs
- The chat template enables tool calling message formatting (tool definitions injected into prompt, tool_calls parsed from output)

**Notes**: To be filled later in the process.

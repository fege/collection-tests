---
test_case_id: TC-CFG-002
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-CFG-002: Validate chatTemplate path loads correctly for each in-scope model

**Objective**: Confirm that the `chatTemplate` path from each model's `servingConfig.toolCalling` points to a valid Jinja template that vLLM can load without errors.

**Test Steps**:
1. For each in-scope model, extract the `chatTemplate` path from `servingConfig.toolCalling` in the CatalogModel
2. Start vLLM with the chat template path:
   ```bash
   vllm serve <model-path> --chat-template=<chatTemplate-path> --enable-auto-tool-choice
   ```
3. Check vLLM startup logs for template loading
4. Verify that vLLM does NOT emit a `FileNotFoundError` or template parse error
5. Repeat for all in-scope models

**Expected Results**:
- vLLM starts successfully with the `chatTemplate` path for each model
- No `FileNotFoundError`, `TemplateNotFound`, or Jinja parse errors appear in logs
- The chat template is loaded and active as shown in startup log output

**Notes**: To be filled later in the process.

---
test_case_id: TC-CFG-001
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-CFG-001: Validate toolCallParser value is recognized by vLLM for each in-scope model

**Objective**: Confirm that the `toolCallParser` value from each model's `servingConfig.toolCalling` is a valid, recognized parser name in the target Red Hat AI Inference Server (vLLM) version.

**Test Steps**:
1. For each in-scope model, extract the `toolCallParser` value from `servingConfig.toolCalling` in the CatalogModel
2. Start vLLM with the parser value:
   ```bash
   vllm serve <model-path> --tool-call-parser=<toolCallParser-value> --enable-auto-tool-choice
   ```
3. Check vLLM startup logs for parser initialization
4. Verify that vLLM does NOT emit a `KeyError` or "invalid tool call parser" error
5. Repeat for all in-scope models

**Expected Results**:
- vLLM starts successfully with the `toolCallParser` value for each model
- No `KeyError: 'invalid tool call parser: <name>'` errors appear in logs
- The parser is loaded and registered as shown in startup log output

**Notes**: To be filled later in the process.

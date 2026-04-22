---
test_case_id: TC-CFG-001
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-CFG-001: Validate --tool-call-parser value is recognized by vLLM for each in-scope model

**Objective**: Confirm that the `--tool-call-parser` value documented in each model card is a valid, recognized parser name in the target Red Hat AI Inference Server version.

**Test Steps**:
1. For each in-scope model, extract the `--tool-call-parser` value from the model card's "Tool Calling Configuration" section
2. Start vLLM with the documented parser name using:
   ```bash
   vllm serve <model-path> --tool-call-parser=<documented-value> --enable-auto-tool-choice
   ```
3. Check vLLM startup logs for parser initialization
4. Verify that vLLM does NOT emit a `KeyError` or "invalid tool call parser" error

**Expected Results**:
- vLLM starts successfully with the documented `--tool-call-parser` value for each model
- No `KeyError: 'invalid tool call parser: <name>'` errors appear in logs
- The parser is loaded and registered as shown in startup log output

**Notes**: To be filled later in the process.

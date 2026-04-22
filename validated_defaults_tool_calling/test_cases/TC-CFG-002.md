---
test_case_id: TC-CFG-002
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-CFG-002: Validate --reasoning-parser value is recognized by vLLM for each in-scope model

**Objective**: Confirm that the `--reasoning-parser` value documented in each model card is a valid parser name or is correctly marked as not required for models that do not use reasoning parsing.

**Test Steps**:
1. For each in-scope model, extract the `--reasoning-parser` value from the model card's "Tool Calling Configuration" section
2. If a reasoning parser is specified, start vLLM with:
   ```bash
   vllm serve <model-path> --reasoning-parser=<documented-value> --tool-call-parser=<documented-value> --enable-auto-tool-choice
   ```
3. If no reasoning parser is specified, verify the model card explicitly states it is not required
4. Check vLLM startup logs for any parser-related errors

**Expected Results**:
- vLLM starts successfully with the documented `--reasoning-parser` value (when specified)
- No parser-related errors appear in startup logs
- Models that do not require a reasoning parser have this clearly documented

**Notes**: To be filled later in the process.

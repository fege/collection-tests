---
test_case_id: TC-NEG-002
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-NEG-002: Verify serving a model with wrong --tool-call-parser produces a detectable failure

**Objective**: Confirm that using an incorrect `--tool-call-parser` value (different from the validated default) results in a detectable failure rather than silent incorrect behavior.

**Preconditions**:
- One in-scope model is available on target hardware
- The validated parser name is known from the model card

**Test Steps**:
1. Start vLLM with an intentionally wrong `--tool-call-parser` value (e.g., use `hermes` for a model that requires `mistral`, or use a non-existent parser name like `invalid_parser_name`)
2. If vLLM starts, send a tool calling request with a simple tool definition
3. Examine the response for signs of incorrect parsing:
   - Empty `tool_calls` array
   - Malformed `function.arguments`
   - `finish_reason` is not `"tool_calls"`
   - Parser error in server logs

**Expected Results**:
- vLLM either fails to start (with a clear error message about the invalid parser) OR
- The tool calling response is detectably wrong (empty `tool_calls`, malformed output, or parsing errors in logs)
- The failure mode is NOT silent — a user can distinguish between "working" and "misconfigured"

**Notes**: To be filled later in the process.

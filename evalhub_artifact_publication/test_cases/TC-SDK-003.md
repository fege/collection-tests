---
test_case_id: TC-SDK-003
source_key: RHAISTRAT-1525
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-SDK-003: DefaultCallbacks behavior inherited by all three adapters

**Objective**: Confirm that lm-evaluation-harness, GuideLLM, and LightEval adapters all inherit artifact URI resolution and failure reporting from DefaultCallbacks without adapter-specific code.

**Preconditions**:
- All three adapter images rebuilt with eval-hub-sdk v0.1.5
- MLflow Tracking Server accessible

**Test Steps**:
1. Run an evaluation job using the lm-evaluation-harness adapter with MLflow tracking enabled
2. Verify the job status response contains `artifact_uris`
3. Repeat step 1-2 with the GuideLLM adapter
4. Repeat step 1-2 with the LightEval adapter
5. Compare the status event payload structure across all three adapters

**Expected Results**:
- All three adapters produce identical `artifact_uris` payload structure
- No adapter-specific code was needed for URI resolution
- DefaultCallbacks handles all artifact metadata capture uniformly

**Notes**: To be filled later in the process.

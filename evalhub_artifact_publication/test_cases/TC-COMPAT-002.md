---
test_case_id: TC-COMPAT-002
source_key: RHAISTRAT-1525
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-COMPAT-002: Old SDK (v0.1.2) status events accepted by new server

**Objective**: Verify that the new EvalHub server tolerates status events from adapters running the older eval-hub-sdk v0.1.2 that do not include artifact_uris in the payload.

**Preconditions**:
- EvalHub server running v0.3.0 (with artifact_uris support)
- Adapter image built with eval-hub-sdk v0.1.2 (old version)

**Test Steps**:
1. Run an evaluation job using an adapter with eval-hub-sdk v0.1.2
2. Wait for the job to complete
3. Query GET /api/v1/evaluations/jobs/{id}
4. Verify status is `Complete` (not an error state)
5. Verify `artifact_uris` is absent or empty in the response

**Expected Results**:
- Server accepts status events without artifact_uris field
- Job completes normally with `Complete` status
- No errors from missing artifact metadata

**Notes**: To be filled later in the process.

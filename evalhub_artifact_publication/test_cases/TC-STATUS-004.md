---
test_case_id: TC-STATUS-004
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-STATUS-004: CompleteWithWarnings is a terminal state with no further transitions

**Objective**: Verify that CompleteWithWarnings is a terminal state and no further state transitions are permitted after it is reached.

**Test Steps**:
1. Submit an evaluation job that will result in partial artifact failure
2. Wait for status to reach `CompleteWithWarnings`
3. Attempt to POST a new status event (e.g., retry or re-upload) to the job
4. Verify the server rejects the transition with an appropriate error
5. Query GET /api/v1/evaluations/jobs/{id} and confirm status remains `CompleteWithWarnings`

**Expected Results**:
- POST status event to a CompleteWithWarnings job returns an error (e.g., 409 Conflict or 400 Bad Request)
- Job status remains `CompleteWithWarnings` — no further transitions allowed

**Notes**: To be filled later in the process.

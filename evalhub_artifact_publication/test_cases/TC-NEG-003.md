---
test_case_id: TC-NEG-003
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-NEG-003: Batch request with invalid job IDs handles partial failures

**Objective**: Verify that a batch request containing a mix of valid and invalid job IDs returns appropriate results for valid IDs and error entries for invalid ones.

**Test Steps**:
1. Create 3 valid evaluation jobs and wait for completion
2. POST to /api/v1/evaluations/jobs/batch with 5 job IDs: 3 valid + 2 non-existent
3. Verify the response contains entries for all 5 job IDs
4. Verify the 3 valid jobs have correct status and artifact_uris
5. Verify the 2 invalid jobs have error entries indicating the job was not found
6. Verify the HTTP status code is appropriate (200 with per-item errors, or 207 Multi-Status)

**Expected Results**:
- Valid job IDs return correct status objects
- Invalid job IDs return error entries (not silently omitted)
- The entire batch does not fail because of individual invalid IDs

**Test Data**:
```json
{
  "job_ids": ["job-valid-001", "job-valid-002", "job-valid-003", "job-nonexistent-001", "job-nonexistent-002"]
}
```

**Notes**: To be filled later in the process.

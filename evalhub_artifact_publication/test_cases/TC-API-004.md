---
test_case_id: TC-API-004
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-API-004: Batch endpoint returns multiple job statuses with artifact URIs

**Objective**: Verify that POST /api/v1/evaluations/jobs/batch accepts a list of job IDs and returns an array of job status objects with artifact_uris for each job.

**Preconditions**:
- EvalHub server running with batch endpoint support
- Multiple completed evaluation jobs with artifact metadata in PostgreSQL

**Test Steps**:
1. Create 5 evaluation jobs with varying artifact counts (0, 1, 2, 3, 5 artifacts)
2. Wait for all jobs to complete
3. POST to /api/v1/evaluations/jobs/batch with all 5 job IDs in the request body
4. Verify the response is an array of 5 job status objects
5. Verify each job status object contains the correct `artifact_uris` for that job
6. Verify the job with 0 artifacts has an empty `artifact_uris` list
7. Verify per-artifact metadata (path, uri, content_type, status) is correct for each job

**Expected Results**:
- Batch endpoint returns all 5 job statuses in a single response
- Each job's artifact_uris matches what GET /api/v1/evaluations/jobs/{id} would return individually
- Response structure is an array of standard job status objects

**Test Data**:
```json
{
  "job_ids": ["job-001", "job-002", "job-003", "job-004", "job-005"]
}
```

**Notes**: To be filled later in the process.

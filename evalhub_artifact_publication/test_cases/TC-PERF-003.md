---
test_case_id: TC-PERF-003
source_key: RHAISTRAT-1525
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-PERF-003: Batch endpoint latency within 200ms for 50 job IDs

**Objective**: Verify that POST /api/v1/evaluations/jobs/batch p99 latency remains under 200ms for requests containing up to 50 job IDs.

**Preconditions**:
- PostgreSQL populated with 50+ completed evaluation jobs containing artifact metadata
- Performance testing tool configured

**Test Steps**:
1. Prepare a batch request with 50 valid job IDs
2. Run 500 batch requests to POST /api/v1/evaluations/jobs/batch
3. Measure p99 latency across all requests
4. Repeat with batch sizes of 1, 10, 25, and 50 job IDs
5. Verify p99 latency is under 200ms for all batch sizes up to 50

**Expected Results**:
- p99 latency < 200ms for batch of 50 job IDs
- Latency scales sub-linearly with batch size (PostgreSQL query optimization)

**Notes**: To be filled later in the process.

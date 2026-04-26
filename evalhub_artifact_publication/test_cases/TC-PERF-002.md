---
test_case_id: TC-PERF-002
source_key: RHAISTRAT-1525
priority: P2
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-PERF-002: POST events latency increase within NFR threshold

**Objective**: Verify that the extended status event payload (with artifact metadata) does not increase POST /api/v1/evaluations/jobs/{id}/events p99 latency by more than 100ms.

**Preconditions**:
- Baseline p99 latency measurement taken before the schema change

**Test Steps**:
1. Record baseline p99 latency for POST events endpoint (pre-change)
2. Deploy the updated EvalHub server
3. POST 500 status events with artifact_uris metadata (2-5 artifacts per event)
4. Measure p99 latency
5. Calculate the delta from baseline

**Expected Results**:
- p99 latency increase is < 100ms compared to baseline

**Notes**: To be filled later in the process.

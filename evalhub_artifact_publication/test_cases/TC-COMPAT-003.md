---
test_case_id: TC-COMPAT-003
source_key: RHAISTRAT-1525
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-COMPAT-003: API schema changes are additive-only (openapi-diff)

**Objective**: Verify that the OpenAPI spec changes for artifact_uris are additive-only — no removed or renamed fields — ensuring existing API clients are not broken.

**Test Steps**:
1. Obtain the OpenAPI spec from the previous EvalHub server version (v0.2.x)
2. Obtain the OpenAPI spec from the new EvalHub server version (v0.3.0)
3. Run `openapi-diff old-spec.yaml new-spec.yaml`
4. Verify all changes are classified as "added" — no "removed" or "changed" entries
5. Verify `artifact_uris` field appears as a new addition to the job status response
6. Verify `CompleteWithWarnings` appears as a new enum value for the status field

**Expected Results**:
- openapi-diff reports only additive changes
- No breaking changes detected
- Existing client code that ignores unknown fields continues to function

**Notes**: To be filled later in the process.

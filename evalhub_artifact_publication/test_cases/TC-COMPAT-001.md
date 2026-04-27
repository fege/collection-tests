---
test_case_id: TC-COMPAT-001
source_key: RHAISTRAT-1525
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-26'
---
# TC-COMPAT-001: PostgreSQL schema migration succeeds without data loss

**Objective**: Verify that the database migration adding artifact_uris and per-artifact outcome columns succeeds and preserves all existing job records.

**Preconditions**:
- PostgreSQL with existing evaluation job records (pre-migration schema)

**Test Steps**:
1. Count existing job records: `SELECT count(*) FROM evaluation_jobs`
2. Run the schema migration to add artifact_uris field
3. Count job records after migration — must match pre-migration count
4. Query an existing job record and verify all original fields are intact
5. Verify the `artifact_uris` field defaults to NULL or empty for pre-existing records
6. Create a new job with artifact metadata and verify it persists correctly

**Expected Results**:
- Migration completes without errors
- No data loss — existing records preserved
- Pre-existing records have NULL/empty artifact_uris
- New records can store artifact metadata

**Validation**:
- `SELECT count(*) FROM evaluation_jobs` matches pre-migration count
- `SELECT artifact_uris FROM evaluation_jobs WHERE id = '<pre_existing_id>'` returns NULL or empty

**Notes**: To be filled later in the process.

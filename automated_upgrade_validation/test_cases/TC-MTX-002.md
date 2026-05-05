---
test_case_id: TC-MTX-002
source_key: RHAISTRAT-1519
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-05-04'
---
# TC-MTX-002: Upgrade matrix defines supported paths declaratively

**Objective**: Verify that the upgrade matrix configuration is declarative and contains all required fields for each supported upgrade path.

**Preconditions**:
- Upgrade matrix configuration file is accessible

**Test Steps**:
1. Read the upgrade matrix configuration
2. Verify each entry defines a from-version and to-version
3. Verify the matrix includes all formally confirmed upgrade paths (e.g., 2.25→3.5, 3.4→3.5)
4. Verify the configuration format supports adding new paths without code changes

**Expected Results**:
- Each matrix entry has a from-version and to-version field
- All confirmed upgrade paths are present in the matrix
- The configuration format is declarative (YAML, JSON, or similar) and does not require compiled code changes to modify

**Notes**: To be filled later in the process.

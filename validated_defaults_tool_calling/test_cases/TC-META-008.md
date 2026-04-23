---
test_case_id: TC-META-008
source_key: RHAISTRAT-1473
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-23'
---
# TC-META-008: Verify backward compatibility — existing API consumers unaffected by optional servingConfig

**Objective**: Confirm that existing API consumers not reading `servingConfig` are unaffected by the new optional field addition to `CatalogModel`.

**Preconditions**:
- AI Hub catalog service deployed with `ServingConfig` schema additions
- Models loaded with and without `servingConfig`

**Test Steps**:
1. Retrieve a model with `servingConfig`: `GET /api/catalog/models/{modelId}`
2. Parse the response using a client that does NOT expect `servingConfig` (ignore unknown fields)
3. Verify all pre-existing fields (`name`, `provider`, `description`, `tasks`, `artifacts`, `customProperties`) are unchanged
4. Retrieve a model without `servingConfig`
5. Verify the response is identical to what it would have been before the schema addition
6. Verify that `customProperties.validated_on` is still present and accessible independently of `servingConfig`

**Expected Results**:
- Adding `servingConfig` does not change the shape or values of any existing fields
- Clients that ignore unknown fields continue to work without modification
- The `servingConfig` field is purely additive

**Notes**: To be filled later in the process.

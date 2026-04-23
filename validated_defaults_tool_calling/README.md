# Validated Defaults for Tool Calling Configuration

Validated vLLM tool calling configurations surfaced through a structured `servingConfig.toolCalling` API schema on `CatalogModel`, enabling programmatic access and Dashboard rendering of validated serving parameters.

## Links

- **Strategy**: [RHAISTRAT-1473](https://redhat.atlassian.net/browse/RHAISTRAT-1473)
- **ADR**: [Serving Configuration for Model Catalog — Tool Calling](https://redhat.atlassian.net/browse/RHAISTRAT-1473)
- **Related RFE**: [RHAIRFE-1428](https://redhat.atlassian.net/browse/RHAIRFE-1428) — Validated Defaults for Tool Calling Configuration (Stage 1)
- **Parent Outcome**: [RHAISTRAT-1357](https://redhat.atlassian.net/browse/RHAISTRAT-1357) — Make Red Hat Inference Server the Best for Agents

## Test Plan

- [TestPlan.md](TestPlan.md) — Full test plan for the feature
- **Version**: 2.1.0
- **Quality Score**: 10/10 — Ready

## Test Cases

- [test_cases/INDEX.md](test_cases/INDEX.md) — Complete test case index
- **31 test cases**: 16x P0, 12x P1, 3x P2
- Categories: META (schema & metadata validation), CFG (configuration correctness), E2E (end-to-end round-trips), NEG (negative testing), PROC (process documentation)

## Test Implementation

Automated tests will be implemented in the appropriate downstream E2E test repository, covering:
- `ServingConfig`/`ToolCallingConfig` OpenAPI schema validation
- Catalog API response validation (`servingConfig.toolCalling` presence/absence)
- Database serialization round-trip (`serving_config` JSON property)
- Model-metadata-collection pipeline output validation
- End-to-end tool calling round-trips on target hardware

## Changelog

### v2.1.0 (2026-04-23)
- Test cases regenerated: 31 TCs across 5 categories (up from 24)
- New TCs for schema validation, API response, database serialization, pipeline output, backward compatibility, catalog-to-deployment E2E, malformed JSON handling, data inconsistency

### v2.0.0 (2026-04-23)
- Updated with ADR: Serving Configuration for Model Catalog — Tool Calling
- Added `ServingConfig`/`ToolCallingConfig` schema, catalog API endpoints, database serialization
- Restructured Section 4 from CLI-focused to schema/API-focused
- Updated NFR with performance/scalability considerations
- Added 3 new risks from ADR
- Resolved 7 of 14 gaps (10 remain open)

### v1.1.0 (2026-04-22)
- Test cases generated: 24 TCs across 5 categories

### v1.0.0 (2026-04-22)
- Initial test plan

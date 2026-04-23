---
feature: validated_defaults_tool_calling
source_key: RHAISTRAT-1473
status: Open
gap_count: 10
last_updated: '2026-04-23'
---
# Gaps -- Validated Defaults for Tool Calling Configuration

## Resolved Gaps

### Scope & Endpoints
- ~~No specification of model card schema or file format~~ — **Resolved by**: ADR `ToolCallingConfig` OpenAPI schema with typed fields (`supported`, `toolCallParser`, `chatTemplate`, `enableAutoToolChoice`, `requiredArgs`) in `api/openapi/src/catalog.yaml`
- ~~No sample tool calling scenarios or expected outputs~~ — **Resolved by**: ADR provides complete YAML catalog input examples, API response JSON, and database property format
- ~~Exact format and location of model cards in model-metadata-collection repo not defined~~ — **Resolved by**: ADR defines YAML catalog format with `servingConfig` as top-level field per model, output as `metadata.yaml`
- ~~Process for accessing validated configs (UI, API, direct repo access) not defined~~ — **Resolved by**: ADR specifies catalog API (`GET /api/catalog/models/{modelId}` with `servingConfig`), Dashboard UI consumption, `filterQuery` with `tasks=tool-calling`

### Test Strategy & Risks
- ~~Model-metadata-collection schema and publishing process are not defined~~ — **Resolved by**: ADR defines YAML catalog format, `servingConfig` top-level field, pipeline changes (output in `metadata.yaml`, remove Markdown from README), component impact table with 8 affected components
- ~~Version-specific parser behavior tracking~~ — **Resolved** by feature refinement (configurations tied to specific Red Hat AI Inference Server versions via `validatedOn` custom property, e.g., `["rhoai-3.5","vllm-0.20.0"]`)
- ~~Disconnected environment requirements~~ — **Resolved** by feature refinement (metadata delivered in catalog container image, `servingConfig` stored in local Properties table)

## Unresolved Gaps

### Scope & Endpoints
- No API specification for vLLM tool calling request/response format (`/v1/chat/completions`) — **Note**: ADR resolves catalog API schema but vLLM runtime API format remains inferred from strategy and OpenAI compatibility — would be resolved by: **vLLM API specification or OpenAI compatibility reference doc**
- No definition of "target hardware profile" for each model — would be resolved by: **Design doc or infrastructure specification mapping models to validated hardware configurations (GPU types, VRAM, node count)**
- No error handling specification for malformed `serving_config` JSON in database or invalid `ToolCallingConfig` data — would be resolved by: **Design doc specifying error handling strategy for corrupted/malformed data in `convertModelProperties()` and `mapDBModelToAPIModel()`**

### Test Strategy & Risks
- ~~Validation checklist and acceptance criteria for "what validated means"~~ — **PARTIALLY RESOLVED**: ADR describes configurations as "tested end-to-end (tool call request, correct tool_calls output, tool response round-trip) on the target hardware." **Still missing**: Concrete pass/fail criteria (e.g., what constitutes a "correct" `tool_calls` output, how many test scenarios, error tolerance) — would be resolved by: **Testing/validation runbook or acceptance criteria document**
- Target hardware profiles for validation are not enumerated — would be resolved by: **Feature refinement or design doc listing the specific hardware configurations (GPU types, VRAM, node count, CUDA versions) per model**
- No process for flagging stale configurations when new inference server versions are released — **Note**: ADR introduces `validatedOn` custom property for version context but no automated staleness detection — would be resolved by: **Design doc or CI/CD specification describing automated staleness checks and re-validation triggers**

### Environment & Infrastructure
- Target OpenShift version unspecified — **Note**: ADR `validatedOn` example suggests `rhoai-3.5` and `vllm-0.20.0` but does not confirm OCP version — would be resolved by: **RHOAI 3.5 compatibility matrix**
- Hardware profile requirements (GPU type, VRAM, node count) not defined per model — would be resolved by: **PSAP hardware validation docs**
- Model storage backend (S3 vs PVC) and access configuration not specified — would be resolved by: **Design doc or infrastructure specification**
- `ToolCallingConfig` schema validation: when `supported=true`, no enforcement that `toolCallParser` and `chatTemplate` are present — would be resolved by: **OpenAPI schema validation rules or ADR addendum defining required-when-supported constraints**

## New Gaps Identified (from ADR)

### Scope & Endpoints
- Dashboard UI rendering implementation not specified — ADR states Dashboard will "consume `servingConfig.toolCalling` from API to render Tool Calling Configuration UI section" but provides no UI component name, rendering logic, or user interaction flows — would be resolved by: **Dashboard design doc or frontend implementation spec**
- `requiredArgs` array format validation rules not specified — ADR does not document whether entries must be validated as complete CLI arguments (e.g., must start with `--`) or are free-form strings — would be resolved by: **API spec or pipeline validation rules**

### Environment & Infrastructure
- Database connection details (PostgreSQL version, connection string format) not specified — ADR references Properties table but no database version requirements — would be resolved by: **AI Hub infrastructure documentation**

## Statistics

- **Total gaps before**: 14 (including 3 previously resolved)
- **Gaps resolved by ADR**: 7 (4 scope, 3 strategy)
- **Gaps remaining (unresolved)**: 10
- **New gaps identified**: 3
- **Total open gaps now**: 10
- **Status**: Open (gaps remaining)

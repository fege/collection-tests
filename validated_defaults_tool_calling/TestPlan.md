---
feature: validated_defaults_tool_calling
source_key: RHAISTRAT-1473
version: 2.1.0
status: In Review
author: RHOAI QE
additional_docs:
- 'ADR: Serving Configuration for Model Catalog — Tool Calling'
last_updated: '2026-04-23'
source_type: null
reviewers: []
components: []
---
# Validated Defaults for Tool Calling Configuration Test Plan
**RHOAI QE – Tool Calling Configuration Validation**

**Strategy**: [RHAISTRAT-1473](https://redhat.atlassian.net/browse/RHAISTRAT-1473)

---

## 1. Executive Summary

### 1.1 Purpose

This test plan validates that supported models in the RHAI model catalog contain accurate, complete, and working tool calling configurations surfaced through a structured `servingConfig.toolCalling` API schema on `CatalogModel`. Users deploying these models for tool calling should be able to retrieve validated vLLM serve arguments programmatically via the catalog API — or copy-paste from the Dashboard UI — and achieve working tool calling on first attempt, eliminating the current trial-and-error configuration process that is the #1 source of support escalations.

The feature addresses a critical gap where known-good configurations for `--tool-call-parser`, `--reasoning-parser`, `--chat-template`, and `--enable-auto-tool-choice` are scattered across upstream docs, GitHub issues, and tribal knowledge. The ADR ([Serving Configuration for Model Catalog — Tool Calling](https://redhat.atlassian.net/browse/RHAISTRAT-1473)) introduces a typed `ServingConfig` container object on `CatalogModel` with an initial `ToolCallingConfig` schema, replacing unstructured Markdown with programmatically accessible, schema-validated configuration. The model-metadata-collection pipeline extracts tool-calling data from HuggingFace YAML frontmatter into `servingConfig.toolCalling` fields, which are serialized as JSON in the model-registry Properties table and served via the catalog API. This is a Tech Preview feature targeting RHOAI 3.5 and aligns with Summit delivery for PSAP-2219 (vLLM optimal config recipes).

### 1.2 Scope

#### In Scope (RHOAI QE Responsibilities)
- `ToolCallingConfig` schema validation: verify OpenAPI schema additions (`ServingConfig`, `ToolCallingConfig`) in `api/openapi/src/catalog.yaml` correctly define required/optional fields, types, and examples
- YAML catalog input parsing: validate model-metadata-collection pipeline outputs `servingConfig.toolCalling` in `metadata.yaml` from HuggingFace YAML frontmatter
- API response validation: confirm `GET /api/catalog/models/{modelId}` returns typed `servingConfig.toolCalling` when present and omits field when absent
- Database serialization round-trip: verify `convertModelProperties()` in `yaml_catalog.go` serializes `servingConfig` to `serving_config` JSON property, and `mapDBModelToAPIModel()` in `db_catalog.go` deserializes correctly
- `tasks` array consistency: ensure models with `servingConfig.toolCalling` include `tool-calling` in their `tasks` array
- Model card content validation for the initial model scope (Qwen/Qwen3-235B-A22B-GPTQ-Int4, openai/gpt-oss-120b, nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4, mistralai/Mistral-Small-4-119B-2603, Llama-3.3-70B-Instruct)
- Correctness of vLLM CLI arguments (`--tool-call-parser`, `--chat-template`, `--enable-auto-tool-choice`, plus `requiredArgs`) in `ToolCallingConfig` fields
- End-to-end validation that published configurations produce working tool calling (tool call request -> correct tool_calls output -> tool response round-trip)
- Validation on target hardware profiles specified per model
- Absence of `servingConfig` field for models without validated tool calling configs (no partial or unvalidated data published)
- Removal of tool-calling Markdown section from model README rendering (replaced by structured API data)
- Backward compatibility: existing API consumers unaffected by new optional `servingConfig` field

#### Out of Scope (Other Teams)
- Runtime auto-detection of parser arguments (deferred to Stage 2 -- separate RFE)
- Dashboard UI rendering of `servingConfig.toolCalling` (Dashboard team responsibility; this plan validates API contract only)
- Performance tuning parameters (covered by PSAP-2219/PSAP-2221)
- Models outside the initial scope list
- vLLM parser behavior changes across versions (requires separate re-validation process)
- Validation of models not yet deployed on target hardware
- `servingConfig` on `BaseModel` or `CatalogModelArtifact` (rejected alternatives per ADR)
- Future `recommendedConfigs` extension (PSAP-2219/2221, separate schema addition)

### 1.3 Test Objectives
1. Verify `ServingConfig` and `ToolCallingConfig` OpenAPI schema additions correctly define required/optional fields, types, defaults, and examples in `api/openapi/src/catalog.yaml`
2. Validate that YAML catalog input with `servingConfig.toolCalling` is correctly parsed by `convertModelProperties()` and serialized as `serving_config` JSON property in the database
3. Confirm that `mapDBModelToAPIModel()` correctly deserializes `serving_config` property to typed `ServingConfig` in API responses, and omits the field when absent
4. Verify that each model in the initial scope has a `servingConfig.toolCalling` entry with correct `toolCallParser`, `chatTemplate`, `enableAutoToolChoice`, and `requiredArgs` values
5. Ensure that models without validated tool calling configs omit `servingConfig` entirely — no partial or unvalidated data is published
6. Validate that models with `servingConfig.toolCalling` include `tool-calling` in their `tasks` array for capability discovery via `filterQuery`
7. Verify that the model-metadata-collection pipeline outputs `servingConfig` in `metadata.yaml` and removes the tool-calling Markdown section from README rendering
8. Validate that each published configuration achieves working tool calling on first attempt on target hardware (tool call request -> correct `tool_calls` output -> tool response round-trip) without silent failures
9. Confirm backward compatibility: existing API consumers not reading `servingConfig` are unaffected by the new optional field

---

## 2. Test Strategy

### 2.1 Test Levels
- **API Integration Testing** - Validate OpenAPI schema additions (`ServingConfig`, `ToolCallingConfig`), catalog API response format, and serialization/deserialization of `servingConfig` between YAML, JSON, and database
- **Data Validation Testing** - Verify that `ToolCallingConfig` fields contain complete and correctly formatted values matching validated vLLM arguments; verify JSON schema compliance for `serving_config` property storage/retrieval
- **Functional Testing** - Test that the model-metadata-collection pipeline correctly outputs `servingConfig` in `metadata.yaml`, removes Markdown README sections, and ensures `tasks` array consistency; test that published vLLM serve commands work end-to-end
- **Integration Testing** - Validate the tool calling workflow (request -> tool_calls output -> tool response round-trip) using configurations retrieved from the catalog API against target hardware
- **UI Testing** - Dashboard team responsibility; QE validates API contract only (verify `servingConfig.toolCalling` is correctly served by the catalog API for Dashboard consumption)

### 2.2 Test Types
- **Positive Testing** - Validate that known-good configurations from `servingConfig.toolCalling` enable successful tool calling on first attempt for all supported models; validate correct API responses with typed `ServingConfig`
- **Negative Testing** - Verify that models without validated configurations omit `servingConfig` entirely (no partial data); verify behavior when `serving_config` JSON is malformed in database; verify invalid flag combinations are not accepted by the schema
- **Boundary Testing** - Test edge cases including models with `requiredArgs` array (additional CLI arguments beyond core flags), models with `enableAutoToolChoice` set to false, empty `requiredArgs` array, `supported: false` with other fields omitted
- **Regression Testing** - Ensure existing models without `servingConfig` remain unaffected; ensure `customProperties` behavior is unchanged; verify backward compatibility of optional fields for existing API consumers

### 2.3 Test Priorities
- **P0 (Critical)** - OpenAPI schema validation and API contract compliance for `ServingConfig`/`ToolCallingConfig`; database serialization/deserialization integrity (`yaml_catalog.go` and `db_catalog.go`); end-to-end validation that each published configuration enables working tool calling on target hardware
- **P1 (High)** - YAML catalog loader correctly outputs `servingConfig`; `tasks` array includes `tool-calling` when `servingConfig.toolCalling` is present; Dashboard can consume and render tool calling configuration; correct metadata completeness for all initial scope models
- **P2 (Medium)** - Documentation process validation for adding new models; `requiredArgs` field validation; error handling for malformed configurations; backward compatibility validation for future `recommendedConfigs` extension point

---

## 3. Test Environment

### 3.1 Test Cluster Configuration
- **OpenShift version**: TBD — resolve via RHOAI 3.5 release compatibility matrix (source: RHOAI product docs, expected OCP 4.14+)
- **RHOAI version**: 3.5 (target version per strategy, Tech Preview)
- **Red Hat AI Inference Server (vLLM) version**: TBD — resolve via RHOAI 3.5 release manifest (source: RHOAI 3.5 component version matrix). Must be pinned before test execution because parser behavior can change across vLLM versions.
- **Python runtime**: Python 3.11+ with `openai>=1.0` SDK and `requests` for test client API calls and tool call validation
- **GPU node requirements per model**:

  | Model | Min GPU Type | Min GPU Count | Min VRAM (Total) | Notes |
  |-------|-------------|---------------|------------------|-------|
  | Qwen/Qwen3-235B-A22B-GPTQ-Int4 | A100 80GB | 4 | 320 GB | GPTQ-Int4 quantized; reduced VRAM vs full-precision |
  | openai/gpt-oss-120b | TBD — resolve via PSAP hardware validation docs | TBD | TBD | Requires PSAP team hardware profile confirmation |
  | nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 | H100 80GB | TBD — resolve via PSAP hardware validation docs | TBD | NVFP4 quantized; MoE architecture (12B active) may reduce VRAM |
  | mistralai/Mistral-Small-4-119B-2603 | A100 80GB or H100 80GB | TBD — resolve via PSAP hardware validation docs | TBD | 119B dense model |
  | Llama-3.3-70B-Instruct | A100 80GB | 2 | 160 GB | Smallest model in scope; well-documented hardware requirements |

### 3.2 Test Data Requirements

#### 3.2.1 Sample Tool Calling Request (Canonical Example)

All models use the OpenAI-compatible `/v1/chat/completions` endpoint. The following is the canonical test request format:

```json
{
  "model": "<model-name>",
  "messages": [
    {
      "role": "user",
      "content": "What is the weather in Boston today?"
    }
  ],
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "get_weather",
        "description": "Get the current weather for a location",
        "parameters": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "City and state, e.g. 'Boston, MA'"
            },
            "unit": {
              "type": "string",
              "enum": ["celsius", "fahrenheit"],
              "description": "Temperature unit"
            }
          },
          "required": ["location"]
        }
      }
    }
  ],
  "tool_choice": "auto"
}
```

#### 3.2.2 Expected Tool Calls Response (Success Criteria)

A successful tool calling response must include a `tool_calls` array in the assistant message:

```json
{
  "choices": [
    {
      "message": {
        "role": "assistant",
        "content": null,
        "tool_calls": [
          {
            "id": "call_abc123",
            "type": "function",
            "function": {
              "name": "get_weather",
              "arguments": "{\"location\": \"Boston, MA\"}"
            }
          }
        ]
      },
      "finish_reason": "tool_calls"
    }
  ]
}
```

**Validation criteria**: (1) `tool_calls` array is non-empty, (2) `function.name` matches a defined tool, (3) `function.arguments` is valid JSON matching the tool's parameter schema, (4) `finish_reason` is `"tool_calls"`.

#### 3.2.3 Tool Response Round-Trip (Full Cycle)

After receiving the tool call, submit the tool result and verify the model continues the conversation:

```json
{
  "model": "<model-name>",
  "messages": [
    {"role": "user", "content": "What is the weather in Boston today?"},
    {"role": "assistant", "content": null, "tool_calls": [{"id": "call_abc123", "type": "function", "function": {"name": "get_weather", "arguments": "{\"location\": \"Boston, MA\"}"}}]},
    {"role": "tool", "tool_call_id": "call_abc123", "content": "{\"temperature\": 72, \"unit\": \"fahrenheit\", \"condition\": \"sunny\"}"}
  ],
  "tools": [...]
}
```

**Expected**: The model produces a natural language response incorporating the tool result (e.g., "The weather in Boston is currently 72°F and sunny.").

#### 3.2.4 Configuration Test Data Per Model (via `servingConfig.toolCalling` fields)

| Model | `toolCallParser` | `chatTemplate` | `enableAutoToolChoice` | `requiredArgs` |
|-------|-----------------|----------------|----------------------|----------------|
| RedHatAI/Granite-4.0-H-Small (reference) | `granite` | `opt/app-root/template/tool_chat_template_granite.jinja` | `true` | `["--config_format granite"]` |
| Qwen/Qwen3-235B-A22B-GPTQ-Int4 | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | `true` | TBD |
| openai/gpt-oss-120b | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | `true` | TBD |
| nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | `true` | TBD |
| mistralai/Mistral-Small-4-119B-2603 | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | `true` | TBD |
| Llama-3.3-70B-Instruct | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | `true` | TBD |

> **Note**: Configuration values map directly to `ToolCallingConfig` schema fields defined in the ADR. The Granite-4.0-H-Small model is included as a reference example from the ADR (not in the initial 5-model scope). Remaining values will be populated once the PSAP team completes end-to-end validation on target hardware and published via the model-metadata-collection pipeline.

#### 3.2.5 YAML Catalog Input Test Data (from ADR)

The model-metadata-collection pipeline outputs `servingConfig` as a top-level field on each model. Test data must include:

**Positive test model** (with tool calling):
```yaml
- name: Granite-4.0-H-Small
  provider: Granite Team, IBM
  tasks:
    - text-generation
    - tool-calling
  servingConfig:
    toolCalling:
      supported: true
      toolCallParser: granite
      chatTemplate: opt/app-root/template/tool_chat_template_granite.jinja
      enableAutoToolChoice: true
      requiredArgs:
        - "--config_format granite"
```

**Negative test model** (without tool calling):
```yaml
- name: granite-7b-redhat-lab
  provider: Red Hat
  tasks:
    - text-generation
  # no servingConfig — tool calling not validated for this model
```

#### 3.2.6 API Response Test Data (from ADR)

**Expected API response with `servingConfig`**:
```json
{
  "name": "Granite-4.0-H-Small",
  "tasks": ["text-generation", "tool-calling"],
  "servingConfig": {
    "toolCalling": {
      "supported": true,
      "toolCallParser": "granite",
      "chatTemplate": "opt/app-root/template/tool_chat_template_granite.jinja",
      "enableAutoToolChoice": true,
      "requiredArgs": ["--config_format granite"]
    }
  },
  "customProperties": {
    "validated_on": {
      "metadataType": "MetadataStringValue",
      "string_value": "[\"rhoai-3.5\",\"vllm-0.20.0\"]"
    }
  }
}
```

**Expected API response without `servingConfig`**: field omitted entirely (standard OpenAPI optional field behavior).

#### 3.2.7 Database Property Test Data

| Property Name | IsCustomProperty | Type | Value |
|---------------|------------------|------|-------|
| `serving_config` | `false` | `StringValue` | `{"toolCalling":{"supported":true,"toolCallParser":"granite",...}}` |

The `serving_config` property follows the same JSON-encoded string pattern used for `language` and `tasks` properties.

#### 3.2.8 Negative Test Data
- Models with `servingConfig` omitted entirely (no `serving_config` property row in database)
- Models with `supported: false` in `ToolCallingConfig` (tool calling explicitly not supported)
- Malformed `serving_config` JSON in database (corrupted serialization, truncated JSON)
- YAML catalog input with partial `servingConfig` (e.g., `toolCallParser` present but `chatTemplate` missing)
- Invalid `toolCallParser` values (non-existent parser name)
- `tasks` array with `tool-calling` but no `servingConfig.toolCalling` (inconsistency)
- `servingConfig.toolCalling` present but `tool-calling` missing from `tasks` array (inconsistency)
- Known-invalid runtime configurations for negative testing (wrong parser name, mismatched parser/model combination)

### 3.3 Test Users

| Role | Identity | Required Permissions | Setup |
|------|----------|---------------------|-------|
| **Model deployer** | Service account `tool-call-test-deployer` (created in test namespace) | Create/update/delete `ServingRuntime` and `InferenceService` CRs; get/list `Pods`, `Services`, `Routes` in the test namespace; access model storage (S3 credentials or PVC read) | `oc create sa tool-call-test-deployer -n <test-ns>` + bind `ClusterRole` with ServingRuntime/InferenceService CRUD via `RoleBinding` |
| **API consumer** | Service account `tool-call-test-client` or user token | HTTP POST to `/v1/chat/completions` on the inference endpoint Route; HTTP GET to `/v1/models` for health check | Route must be exposed; if authentication is enabled on the inference endpoint, a bearer token or API key must be provisioned |
| **Repo reader** | GitHub user or token with read access | Read access to `model-metadata-collection` repository for model card content validation | TBD — resolve via team GitHub org membership or personal access token |
| **Cluster admin** (setup only) | Cluster admin user | Install RHOAI operator, configure GPU nodes, create test namespace, assign RBAC | Pre-existing cluster admin; not used during test execution |

---

## 4. Serving Configuration Schema and Endpoints Under Test

| Endpoint/Method | Type | Purpose | Priority |
|-----------------|------|---------|----------|
| `ServingConfig` / `ToolCallingConfig` in `api/openapi/src/catalog.yaml` | OpenAPI Schema | Define typed schemas for `servingConfig.toolCalling` with required/optional fields | P0 |
| `convertModelProperties()` in `yaml_catalog.go` | Go Method | Serialize `servingConfig` to `serving_config` JSON property on model write | P0 |
| `mapDBModelToAPIModel()` in `db_catalog.go` | Go Method | Deserialize `serving_config` property to typed `ServingConfig` on model read | P0 |
| GET `/api/catalog/models/{modelId}` | REST API | Return `CatalogModel` with `servingConfig.toolCalling` when present, omit when absent | P0 |
| `serving_config` property in Properties table | Database | Store `servingConfig` as JSON string (same pattern as `language` and `tasks`) | P0 |
| model-metadata-collection YAML output | Data Pipeline | Generate `servingConfig.toolCalling` block in `metadata.yaml` from HuggingFace frontmatter | P0 |
| `servingConfig.toolCalling.supported` | Config Field | Boolean flag indicating tool-calling support (required field) | P0 |
| `servingConfig.toolCalling.toolCallParser` | Config Field | vLLM `--tool-call-parser` argument value (e.g., `granite`) | P0 |
| `servingConfig.toolCalling.chatTemplate` | Config Field | Path for `--chat-template` argument (e.g., `opt/app-root/template/tool_chat_template_granite.jinja`) | P0 |
| Tool calling request API (`/v1/chat/completions`) | REST API | Accepts tool calling requests with tool definitions using config from `servingConfig` | P0 |
| Tool calling response format | REST API | Returns structured `tool_calls` array in correct format | P0 |
| GET `/api/catalog/models` | REST API | List models with optional `servingConfig` field | P1 |
| `filterQuery` with `tasks=tool-calling` | Query Parameter | Discover models with tool-calling capability via `tasks` array | P1 |
| `servingConfig.toolCalling.enableAutoToolChoice` | Config Field | Boolean for `--enable-auto-tool-choice` flag (default: `true`) | P1 |
| model-metadata-collection README rendering | Data Pipeline | Omit tool-calling Markdown section when `servingConfig` is present | P1 |
| `tasks` array includes `tool-calling` | Data Consistency | Ensure `tool-calling` in `tasks` when `servingConfig.toolCalling` exists | P1 |
| Tool response round-trip | REST API | Processes tool response and continues conversation | P1 |
| `servingConfig.toolCalling.requiredArgs` | Config Field | Array of additional CLI arguments beyond core flags (e.g., `--config_format granite`) | P2 |
| Model-metadata-collection process documentation | Config | Documents process for adding new validated tool calling configs | P2 |

---

## 5. Test Cases

**31 test cases** have been generated across 5 categories.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-META (Schema & Metadata Validation) | 8 | 5x P0, 3x P1 |
| TC-CFG (Configuration Correctness) | 5 | 4x P0, 1x P2 |
| TC-E2E (End-to-End Validation) | 9 | 7x P0, 2x P1 |
| TC-NEG (Negative Testing) | 7 | 7x P1 |
| TC-PROC (Process Documentation) | 2 | 2x P2 |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- **TC-META** - Schema and metadata validation (OpenAPI schema, API response, serialization/deserialization, pipeline output, tasks consistency, backward compatibility)
- **TC-CFG** - Configuration correctness (vLLM CLI argument validation per model via `servingConfig.toolCalling` fields)
- **TC-E2E** - End-to-end tool calling validation (full round-trip on target hardware, catalog API to deployment workflow)
- **TC-NEG** - Negative testing (missing configs, invalid flags, malformed JSON, data inconsistency)
- **TC-PROC** - Process documentation validation (adding new model configs to model-metadata-collection)

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Full tool calling round-trip with Qwen3-235B-A22B-GPTQ-Int4 | servingConfig fields, toolCallParser, chatTemplate, request API, response format, round-trip | P0 |
| TC-E2E-002 | Full tool calling round-trip with gpt-oss-120b | servingConfig fields, toolCallParser, chatTemplate, request API, response format, round-trip | P0 |
| TC-E2E-003 | Full tool calling round-trip with Nemotron-3-Super-120B | servingConfig fields, toolCallParser, chatTemplate, request API, response format, round-trip | P0 |
| TC-E2E-004 | Full tool calling round-trip with Mistral-Small-4-119B | servingConfig fields, toolCallParser, chatTemplate, request API, response format, round-trip | P0 |
| TC-E2E-005 | Full tool calling round-trip with Llama-3.3-70B-Instruct | servingConfig fields, toolCallParser, chatTemplate, request API, response format, round-trip | P0 |
| TC-E2E-006 | Response format matches OpenAI-compatible schema across all models | Request API, response format | P0 |
| TC-E2E-007 | Multi-tool selection scenario | Request API, response format | P1 |
| TC-E2E-008 | Tool calling with streaming enabled | Request API, response format | P1 |
| TC-E2E-009 | Catalog API to vLLM deployment end-to-end | Catalog API, servingConfig, convertModelProperties, mapDBModelToAPIModel, request API, response format, round-trip | P0 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| `ServingConfig` / `ToolCallingConfig` OpenAPI schema | TC-E2E-009 |
| `convertModelProperties()` in `yaml_catalog.go` | TC-E2E-009 |
| `mapDBModelToAPIModel()` in `db_catalog.go` | TC-E2E-009 |
| GET `/api/catalog/models/{modelId}` | TC-E2E-009 |
| `serving_config` property in Properties table | TC-E2E-009 |
| model-metadata-collection YAML output | TC-E2E-009 |
| `servingConfig.toolCalling.supported` | TC-E2E-001 through TC-E2E-005, TC-E2E-009 |
| `servingConfig.toolCalling.toolCallParser` | TC-E2E-001 through TC-E2E-005, TC-E2E-009 |
| `servingConfig.toolCalling.chatTemplate` | TC-E2E-001 through TC-E2E-005, TC-E2E-009 |
| Tool calling request API (`/v1/chat/completions`) | TC-E2E-001 through TC-E2E-009 |
| Tool calling response format | TC-E2E-001 through TC-E2E-009 |
| Tool response round-trip | TC-E2E-001 through TC-E2E-005 |
| Model-metadata-collection process doc | (covered by TC-PROC-001, TC-PROC-002) |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

Tool calling configuration metadata is delivered as part of the catalog data loaded from the model-metadata-collection pipeline. The `servingConfig` field is stored in the model-registry database alongside existing model metadata. No additional connectivity requirements are introduced.

**Testing considerations:**
- Verify that `servingConfig.toolCalling` is accessible via the catalog API in a fully disconnected environment with no external network access
- Confirm that all tool calling metadata is embedded in the catalog YAML at build time (from model-metadata-collection pipeline output)
- Test that the Dashboard does not attempt external API calls or resource fetches to render the Tool Calling Configuration UI section
- Validate that the `serving_config` property is stored locally in the Properties table and does not require network access to deserialize

### 7.2 Upgrade/Migration

The `servingConfig` schema addition is backward-compatible: it uses the existing Properties table for storage (no database migration), and the field is optional on `CatalogModel`. The `validatedOn` custom property provides version context for when the tool-calling configuration was validated.

**Testing considerations:**
- **No database migration required** -- Verify that the `serving_config` property is stored using the existing Properties table pattern; no schema migration is needed
- **Backward compatibility** -- Existing models without `servingConfig` must continue to function with the field absent from API responses; existing API consumers not reading `servingConfig` are unaffected
- **Version-dependent parser behavior** -- Test that each published configuration's `validatedOn` custom property ties it to specific runtime versions (e.g., `["rhoai-3.5","vllm-0.20.0"]`); verify configurations are flagged for re-validation when a new inference server version is released
- **Schema evolution** -- Test that existing `servingConfig.toolCalling` entries remain valid when future fields (e.g., `recommendedConfigs`) are added to `ServingConfig`
- **Migration from Markdown to structured data** -- Validate that the model-metadata-collection pipeline correctly transitions from README Markdown rendering to `servingConfig` YAML output without data loss
- **Rollback scenarios** -- Test that downgrading to a catalog version without `ServingConfig` schema support gracefully ignores the `serving_config` property

### 7.3 Performance/Scalability

The `servingConfig` adds a JSON-serialized property row to the existing Properties table. The serialization/deserialization overhead should be negligible, but should be verified.

**Testing considerations:**
- Measure database read/write latency impact of JSON marshaling/unmarshaling for `serving_config` property in `convertModelProperties()` and `mapDBModelToAPIModel()`
- Validate API response size increase for models with `servingConfig` is negligible compared to models without
- Test catalog API list endpoint performance with mixed models (some with `servingConfig`, some without)
- Ensure `filterQuery` performance on `tasks` array is not degraded by the introduction of `servingConfig`

### 7.4 RBAC/Authorization

**Not Applicable** -- The feature does not introduce new endpoints, resources, or operations requiring authorization. It extends the existing `CatalogModel` schema with an optional field served through the same catalog API endpoints with the same access controls. RBAC for catalog API access remains unchanged.

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| ~~Model-metadata-collection repo may not be the correct vehicle for this feature~~ | ~~High~~ | ~~Medium~~ | **RESOLVED by ADR**: model-metadata-collection pipeline confirmed as the vehicle; outputs `servingConfig` in `metadata.yaml` from HuggingFace frontmatter |
| Validated configs may become stale when vLLM parser behavior changes across versions | High | High | `validatedOn` custom property ties configurations to specific runtime versions (e.g., `["rhoai-3.5","vllm-0.20.0"]`); establish regression testing for re-validation after vLLM updates |
| PSAP team may not have access to target hardware for validation before publication | High | Medium | Define clear ownership and SLA for hardware-based validation; establish fallback process if target hardware is unavailable |
| Dependency on upstream vLLM parser availability (e.g., Granite 4.0h Tiny KeyError with upstream-documented "granite4" parser name) | High | Medium | Establish parser availability verification as part of validation; document parser name discrepancies; coordinate with vLLM upstream |
| Dependency on PSAP team for hardware-based validation creates bottleneck | Medium | High | Align validation schedule with PSAP team's existing hardware test cycles; establish clear SLA and communication channel |
| Dashboard team dependency for UI implementation of `servingConfig.toolCalling` | High | Medium | Provide Dashboard team with API contract and example responses early; document `ToolCallingConfig` schema thoroughly; coordinate release timing |
| model-metadata-collection pipeline changes must be synchronized with AI Hub API deployment | High | Medium | Deploy API schema changes first (additive/optional); pipeline changes to output `servingConfig` deployed after API supports the field |
| JSON serialization errors in `convertModelProperties()` or `mapDBModelToAPIModel()` cause silent data loss | High | Low | Add unit tests for all JSON marshal/unmarshal paths in `yaml_catalog.go` and `db_catalog.go`; validate error handling for malformed `serving_config` |
| `tasks` array and `servingConfig.toolCalling` become inconsistent (one present, other absent) | Medium | Medium | Add validation in YAML catalog loader to ensure `tool-calling` task is added when `servingConfig.toolCalling` is present; document this requirement for pipeline maintainers |
| Incorrect configurations could be published if end-to-end validation is incomplete or skipped | High | Medium | Enforce mandatory end-to-end validation before publication; implement automated validation gate in publishing workflow; maintain validation audit trail |
| `servingConfig` field displayed for models with partial or unvalidated data | Medium | Low | ADR specifies models without validated configs omit `servingConfig` entirely; add automated check in publishing pipeline to prevent partial configs |
| `ToolCallingConfig` schema validation gaps: `supported=true` does not enforce presence of `toolCallParser` and `chatTemplate` | Medium | Medium | Add OpenAPI schema validation rules or pipeline-level validation to ensure configuration completeness when `supported=true` |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure

| Component | Specification | Source for TBD Resolution |
|-----------|--------------|--------------------------|
| OpenShift cluster | TBD — resolve via RHOAI 3.5 compatibility matrix (expected OCP 4.14+) | RHOAI 3.5 product documentation |
| RHOAI version | 3.5 (Tech Preview) | Strategy document |
| Red Hat AI Inference Server (vLLM) | vLLM 0.20.0 (per ADR `validatedOn` example) — confirm via RHOAI 3.5 release manifest | RHOAI 3.5 release manifest, ADR `validatedOn` example |
| AI Hub catalog service | Deployed with `ServingConfig`/`ToolCallingConfig` OpenAPI schema additions | AI Hub deployment |
| Model-registry database | PostgreSQL with Properties table for `serving_config` JSON property storage | AI Hub deployment |
| GPU nodes | Per-model hardware profile (see Section 3.1 GPU table); minimum 4x A100 80GB or equivalent for the largest model (Qwen3-235B-A22B-GPTQ-Int4) | PSAP hardware validation docs |
| Model storage | S3-compatible object storage (e.g., ODF/NooBaa, MinIO, AWS S3) or PVC with ReadOnlyMany access containing model weights for all 5 models | Cluster provisioning playbook |
| Network | Route or NodePort exposing vLLM inference endpoints for API access; no external internet required for test execution (models served locally) | Cluster networking configuration |
| model-metadata-collection pipeline | Updated to output `servingConfig` in `metadata.yaml` and remove Markdown README sections | Pipeline deployment |
| Container registry | `registry.redhat.io` access for RHOAI model container images (e.g., `oci://registry.redhat.io/rhai/modelcar-granite-4-0-h-small:3.0`) | Container registry access |

### 9.2 Configuration

- **`servingConfig.toolCalling` fields per model**: See Section 3.2.4 table for `toolCallParser`, `chatTemplate`, `enableAutoToolChoice`, `requiredArgs` values (TBD pending PSAP validation)
- **Environment variables**:
  - `MODEL_PATH` — path to model weights on storage (S3 URI or PVC mount path)
  - `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` — if using S3-compatible storage
  - `INFERENCE_ENDPOINT` — base URL of the vLLM inference service (e.g., `https://<route-host>`)
  - `CATALOG_API_ENDPOINT` — base URL of the AI Hub catalog API (e.g., `https://<catalog-host>/api/catalog`)
- **ServingRuntime / InferenceService manifests**: YAML manifests with validated vLLM arguments from `servingConfig.toolCalling` fields injected as container args; one manifest per model under test. Example structure (using ADR reference values):

  ```yaml
  apiVersion: serving.kserve.io/v1alpha1
  kind: ServingRuntime
  metadata:
    name: vllm-tool-call-test-<model-short-name>
  spec:
    containers:
      - name: kserve-container
        args:
          - "--model=/mnt/models/<model-path>"
          - "--tool-call-parser=granite"
          - "--chat-template=opt/app-root/template/tool_chat_template_granite.jinja"
          - "--enable-auto-tool-choice"
          - "--config_format granite"
  ```

- **Catalog YAML input**: `metadata.yaml` from model-metadata-collection pipeline, containing `servingConfig.toolCalling` block for each model with validated tool calling
- **OpenAPI schema files**: `api/openapi/src/catalog.yaml` with `ServingConfig` and `ToolCallingConfig` schema definitions
- **OpenAPI code generation**: `make -C catalog gen/openapi` and `make gen/openapi-server` for regenerating Go types from schema changes

### 9.3 Test Tools

| Tool | Version | Purpose |
|------|---------|---------|
| `oc` (OpenShift CLI) | 4.14+ (matching cluster version) | Inspect pods, logs, CRs; deploy ServingRuntime/InferenceService; manage service accounts and RBAC |
| `curl` / `httpie` | any | Quick API smoke tests against catalog API (`/api/catalog/models`), vLLM endpoints (`/v1/models`, `/v1/chat/completions`) |
| Python `openai` SDK | >= 1.0 | Primary test client for tool calling requests and response validation (structured access to `tool_calls` array) |
| Python `requests` | >= 2.28 | HTTP client for catalog API testing and raw request/response inspection |
| `jq` | >= 1.6 | Parse and validate JSON response structure from CLI (catalog API responses, `servingConfig` fields) |
| Go testing framework | Go 1.21+ | Unit tests for `yaml_catalog.go` (`convertModelProperties`) and `db_catalog.go` (`mapDBModelToAPIModel`) |
| `make` | any | OpenAPI code generation: `make -C catalog gen/openapi`, `make gen/openapi-server` |
| YAML validation tools | `yamllint` or equivalent | Validate `metadata.yaml` catalog input format |
| `psql` | any | Direct database inspection of `serving_config` property in Properties table (debug only) |
| `oc logs` / OpenShift logging | n/a | Debug silent failures, parser errors, and model loading issues in vLLM pods |

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-META | 8 | 5 | 3 | 0 |
| TC-CFG | 5 | 4 | 0 | 1 |
| TC-E2E | 9 | 7 | 2 | 0 |
| TC-NEG | 7 | 0 | 7 | 0 |
| TC-PROC | 2 | 0 | 0 | 2 |
| **Total** | **31** | **16** | **12** | **3** |

### 10.2 Configuration/Endpoint Coverage

| Endpoint | Test Cases | Coverage |
|----------|------------|----------|
| `ServingConfig` / `ToolCallingConfig` OpenAPI schema | TC-META-001, TC-E2E-009 | |
| `convertModelProperties()` in `yaml_catalog.go` | TC-META-004, TC-E2E-009 | |
| `mapDBModelToAPIModel()` in `db_catalog.go` | TC-META-005, TC-NEG-006, TC-E2E-009 | |
| GET `/api/catalog/models/{modelId}` | TC-META-002, TC-META-003, TC-E2E-009 | |
| `serving_config` property in Properties table | TC-META-004, TC-META-005 | |
| model-metadata-collection YAML output | TC-META-006, TC-E2E-009 | |
| `servingConfig.toolCalling.supported` | TC-META-002 | |
| `servingConfig.toolCalling.toolCallParser` | TC-CFG-001, TC-NEG-002, TC-E2E-001 through TC-E2E-005 | |
| `servingConfig.toolCalling.chatTemplate` | TC-CFG-002, TC-NEG-003, TC-E2E-001 through TC-E2E-005 | |
| `servingConfig.toolCalling.enableAutoToolChoice` | TC-CFG-003, TC-NEG-004 | |
| `servingConfig.toolCalling.requiredArgs` | TC-CFG-004 | |
| GET `/api/catalog/models` | TC-META-007 | |
| `filterQuery` with `tasks=tool-calling` | TC-META-007 | |
| `tasks` array includes `tool-calling` | TC-META-007, TC-NEG-007 | |
| model-metadata-collection README rendering | TC-META-006 | |
| Tool calling request API (`/v1/chat/completions`) | TC-CFG-005, TC-E2E-001 through TC-E2E-009 | |
| Tool calling response format | TC-E2E-006, TC-E2E-007, TC-E2E-008, TC-E2E-009 | |
| Tool response round-trip | TC-E2E-001 through TC-E2E-005 | |
| Model-metadata-collection process doc | TC-PROC-001, TC-PROC-002 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-22 | Initial test plan |
| 1.1.0 | 2026-04-22 | Test cases generated: 24 TCs across 5 categories |
| 2.0.0 | 2026-04-23 | Updated with ADR: Serving Configuration for Model Catalog — Tool Calling. Added `ServingConfig`/`ToolCallingConfig` schema, catalog API endpoints, database serialization, pipeline changes, component impact table. Restructured Section 4 from CLI-focused to schema/API-focused. Updated NFR with performance/scalability considerations. Added 3 new risks from ADR. Resolved 7 gaps. |
| 2.1.0 | 2026-04-23 | Test cases regenerated: 31 TCs across 5 categories (up from 24). New TCs for schema validation (TC-META-004, -005), API response (TC-META-002, -003), pipeline output (TC-META-006), tasks consistency (TC-META-007), backward compatibility (TC-META-008), catalog-to-deployment E2E (TC-E2E-009), malformed JSON (TC-NEG-006), data inconsistency (TC-NEG-007). |

---

**End of Test Plan**

---
feature: validated_defaults_tool_calling
source_key: RHAISTRAT-1473
version: 1.0.0
status: In Review
author: RHOAI QE
additional_docs: []
last_updated: '2026-04-22'
source_type: null
reviewers: []
---
# Validated Defaults for Tool Calling Configuration Test Plan
**RHOAI QE – Tool Calling Configuration Validation**

**Strategy**: [RHAISTRAT-1473](https://redhat.atlassian.net/browse/RHAISTRAT-1473)

---

## 1. Executive Summary

### 1.1 Purpose

This test plan validates that supported models in the RHAI model catalog contain accurate, complete, and working tool calling configurations in their model cards. Users deploying these models for tool calling should be able to copy-paste the documented `vllm serve` command and achieve working tool calling on first attempt, eliminating the current trial-and-error configuration process that is the #1 source of support escalations.

The feature addresses a critical gap where known-good configurations for `--tool-call-parser`, `--reasoning-parser`, `--chat-template`, and `--enable-auto-tool-choice` are scattered across upstream docs, GitHub issues, and tribal knowledge. By providing validated defaults directly in model cards, we reduce time-to-first-success from hours to minutes and eliminate silent failures from misconfiguration. This is a Tech Preview feature targeting RHOAI 3.5 and aligns with Summit delivery for PSAP-2219 (vLLM optimal config recipes).

### 1.2 Scope

#### In Scope (RHOAI QE Responsibilities)
- Model card content validation for the initial model scope (Qwen/Qwen3-235B-A22B-GPTQ-Int4, openai/gpt-oss-120b, nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4, mistralai/Mistral-Small-4-119B-2603, Llama-3.3-70B-Instruct)
- Presence and completeness of "Tool Calling Configuration" section in model cards
- Correctness of vLLM CLI arguments (`--tool-call-parser`, `--reasoning-parser`, `--chat-template`, `--enable-auto-tool-choice`) documented in model cards
- End-to-end validation that documented configurations produce working tool calling (tool call request -> correct tool_calls output -> tool response round-trip)
- Validation on target hardware profiles specified per model
- Absence of "Tool Calling Configuration" section for models without validated configs
- Documentation of the process for adding new validated configs to the model-metadata-collection repo

#### Out of Scope (Other Teams)
- Runtime auto-detection of parser arguments (deferred to Stage 2 -- separate RFE)
- UI changes to the model catalog (no UI capacity before Summit per PSAP-2219)
- Performance tuning parameters (covered by PSAP-2219/PSAP-2221)
- Models outside the initial scope list
- vLLM parser behavior changes across versions (requires separate re-validation process)
- Validation of models not yet deployed on target hardware

### 1.3 Test Objectives
1. Verify that each model in the initial scope has a "Tool Calling Configuration" section in its model card with complete vLLM serve arguments
2. Confirm that each published configuration includes all four required flags (`--tool-call-parser`, `--reasoning-parser`, `--chat-template`, `--enable-auto-tool-choice`) with correct values
3. Validate that a user following the documented model card instructions can serve each model and achieve working tool calling on first attempt without modification
4. Ensure that model cards without validated tool calling configurations do not display a "Tool Calling Configuration" section
5. Verify that each published configuration has been validated end-to-end on the target hardware profile for that model
6. Confirm that the model-metadata-collection repo contains a documented process for adding validated tool calling configs for new models
7. Validate that documented configurations successfully complete a full tool calling round-trip (request -> tool_calls output -> tool response) without silent failures or parsing errors

---

## 2. Test Strategy

### 2.1 Test Levels
- **Data Validation Testing** - Verify that model card metadata contains complete and correctly formatted tool calling configurations
- **Functional Testing** - Test that published vLLM serve commands with tool calling arguments work end-to-end for each supported model
- **Integration Testing** - Validate the tool calling workflow (request -> tool_calls output -> tool response round-trip) against the target hardware profile
- **UI Testing** - Verify that model cards display the "Tool Calling Configuration" section only when validated configs exist, and do not show the section when configs are missing or unvalidated

### 2.2 Test Types
- **Positive Testing** - Validate that known-good configurations from model cards enable successful tool calling on first attempt for all supported models
- **Negative Testing** - Verify that model cards without validated configurations do not display the tool calling section, and that invalid flag combinations are not published
- **Boundary Testing** - Test edge cases including models with multiple parser options, models with interdependent flags, and models with version-specific parser behavior
- **Regression Testing** - Ensure that existing tool calling configurations remain valid after vLLM version updates or model metadata collection schema changes

### 2.3 Test Priorities
- **P0 (Critical)** - End-to-end validation that each published configuration enables working tool calling on target hardware; blocking failures in the model metadata collection publishing process
- **P1 (High)** - Correct metadata formatting and completeness for all initial scope models (Qwen3-235B, gpt-oss-120b, Nemotron-3-Super-120B, Mistral-Small-4, Llama-3.3-70B); accurate display logic for showing/hiding tool calling sections
- **P2 (Medium)** - Documentation process validation for adding new models; error messaging when configurations are incomplete or missing; user experience improvements for copy-paste workflows

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

#### 3.2.4 Configuration Test Data Per Model

| Model | `--tool-call-parser` | `--reasoning-parser` | `--chat-template` | `--enable-auto-tool-choice` |
|-------|---------------------|---------------------|-------------------|---------------------------|
| Qwen/Qwen3-235B-A22B-GPTQ-Int4 | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | Yes |
| openai/gpt-oss-120b | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | Yes |
| nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | Yes |
| mistralai/Mistral-Small-4-119B-2603 | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | Yes |
| Llama-3.3-70B-Instruct | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | TBD — resolve via PSAP validation results | Yes |

> **Note**: The exact validated argument values are the primary deliverable of this feature. They will be populated once the PSAP team completes end-to-end validation on target hardware. These values are then published to the model-metadata-collection repo as the "validated defaults."

#### 3.2.5 Negative Test Data
- Known-invalid configurations for negative testing (wrong parser name, missing `--chat-template`, `--enable-auto-tool-choice` omitted, mismatched parser/model combination)
- Model cards from model-metadata-collection repo with and without "Tool Calling Configuration" sections

### 3.3 Test Users

| Role | Identity | Required Permissions | Setup |
|------|----------|---------------------|-------|
| **Model deployer** | Service account `tool-call-test-deployer` (created in test namespace) | Create/update/delete `ServingRuntime` and `InferenceService` CRs; get/list `Pods`, `Services`, `Routes` in the test namespace; access model storage (S3 credentials or PVC read) | `oc create sa tool-call-test-deployer -n <test-ns>` + bind `ClusterRole` with ServingRuntime/InferenceService CRUD via `RoleBinding` |
| **API consumer** | Service account `tool-call-test-client` or user token | HTTP POST to `/v1/chat/completions` on the inference endpoint Route; HTTP GET to `/v1/models` for health check | Route must be exposed; if authentication is enabled on the inference endpoint, a bearer token or API key must be provisioned |
| **Repo reader** | GitHub user or token with read access | Read access to `model-metadata-collection` repository for model card content validation | TBD — resolve via team GitHub org membership or personal access token |
| **Cluster admin** (setup only) | Cluster admin user | Install RHOAI operator, configure GPU nodes, create test namespace, assign RBAC | Pre-existing cluster admin; not used during test execution |

---

## 4. vLLM Tool Calling Configuration Under Test

| Endpoint/Method | Type | Purpose | Priority |
|-----------------|------|---------|----------|
| Model card metadata file | Config | Contains "Tool Calling Configuration" section with validated vLLM arguments | P0 |
| `vllm serve` with `--tool-call-parser` | CLI | Specifies the parser for tool call output format | P0 |
| `vllm serve` with `--reasoning-parser` | CLI | Specifies the parser for reasoning output (if applicable) | P0 |
| `vllm serve` with `--chat-template` | CLI | Specifies the Jinja chat template for tool calling | P0 |
| `vllm serve` with `--enable-auto-tool-choice` | CLI | Enables automatic tool selection behavior | P0 |
| Tool calling request API (`/v1/chat/completions`) | REST | Accepts tool calling requests with tool definitions | P0 |
| Tool calling response format | REST | Returns structured `tool_calls` array in correct format | P0 |
| Tool response round-trip | REST | Processes tool response and continues conversation | P1 |
| Model-metadata-collection repo process doc | Config | Documents process for adding new validated tool calling configs | P1 |

---

## 5. Test Cases

**24 test cases** have been generated across 5 categories.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-META (Metadata Validation) | 4 | 2x P0, 2x P1 |
| TC-CFG (Configuration Correctness) | 5 | 5x P0 |
| TC-E2E (End-to-End Validation) | 8 | 6x P0, 2x P1 |
| TC-NEG (Negative Testing) | 5 | 5x P1 |
| TC-PROC (Process Documentation) | 2 | 2x P2 |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- **TC-META** - Model card metadata validation (presence, completeness, formatting)
- **TC-CFG** - Configuration correctness (vLLM CLI argument validation per model)
- **TC-E2E** - End-to-end tool calling validation (full round-trip on target hardware)
- **TC-NEG** - Negative testing (missing configs, invalid flags, unvalidated models)
- **TC-PROC** - Process documentation validation (adding new model configs)

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Full tool calling round-trip with Qwen3-235B-A22B-GPTQ-Int4 | Model card, all CLI flags, request API, response format, round-trip | P0 |
| TC-E2E-002 | Full tool calling round-trip with gpt-oss-120b | Model card, all CLI flags, request API, response format, round-trip | P0 |
| TC-E2E-003 | Full tool calling round-trip with Nemotron-3-Super-120B | Model card, all CLI flags, request API, response format, round-trip | P0 |
| TC-E2E-004 | Full tool calling round-trip with Mistral-Small-4-119B | Model card, all CLI flags, request API, response format, round-trip | P0 |
| TC-E2E-005 | Full tool calling round-trip with Llama-3.3-70B-Instruct | Model card, all CLI flags, request API, response format, round-trip | P0 |
| TC-E2E-006 | Response format matches OpenAI-compatible schema across all models | Request API, response format | P0 |
| TC-E2E-007 | Multi-tool selection scenario | Request API, response format | P1 |
| TC-E2E-008 | Tool calling with streaming enabled | Request API, response format | P1 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| Model card metadata file | TC-E2E-001, TC-E2E-002, TC-E2E-003, TC-E2E-004, TC-E2E-005 |
| `vllm serve` with `--tool-call-parser` | TC-E2E-001, TC-E2E-002, TC-E2E-003, TC-E2E-004, TC-E2E-005 |
| `vllm serve` with `--reasoning-parser` | TC-E2E-001, TC-E2E-002, TC-E2E-003, TC-E2E-004, TC-E2E-005 |
| `vllm serve` with `--chat-template` | TC-E2E-001, TC-E2E-002, TC-E2E-003, TC-E2E-004, TC-E2E-005 |
| `vllm serve` with `--enable-auto-tool-choice` | TC-E2E-001, TC-E2E-002, TC-E2E-003, TC-E2E-004, TC-E2E-005 |
| Tool calling request API (`/v1/chat/completions`) | TC-E2E-001 through TC-E2E-008 |
| Tool calling response format | TC-E2E-001 through TC-E2E-008 |
| Tool response round-trip | TC-E2E-001, TC-E2E-002, TC-E2E-003, TC-E2E-004, TC-E2E-005 |
| Model-metadata-collection repo process doc | (covered by TC-PROC-001, TC-PROC-002) |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

Tool calling configuration metadata must be delivered as part of the default catalog container image alongside existing model metadata. No additional connectivity requirements should be introduced.

**Testing considerations:**
- Verify that "Tool Calling Configuration" sections are accessible in the model catalog UI in a fully disconnected environment with no external network access
- Confirm that all tool calling metadata is embedded in the catalog container image at build time
- Test that model card rendering does not attempt external API calls or resource fetches to display tool calling configurations
- Validate that the catalog container image build process includes tool calling metadata from the model-metadata-collection repository

### 7.2 Upgrade/Migration

Validated configurations are static and tied to the specific Red Hat AI Inference Server version they were tested against. When vLLM parser behavior changes across versions, configurations must be re-validated and updated.

**Testing considerations:**
- **Version-dependent parser behavior** -- Test that each published configuration is explicitly tied to a specific Red Hat AI Inference Server version; verify that configurations are flagged for re-validation when a new inference server version is released
- **Configuration staleness detection** -- Validate that the system can identify configurations that have not been re-validated for newer inference server versions
- **Model metadata schema evolution** -- Test that existing "Tool Calling Configuration" sections remain valid if the model-metadata-collection schema is updated
- **Backwards compatibility** -- Verify that model cards published with tool calling configs remain accessible and functional after catalog UI updates
- **Migration testing** -- Validate that models with existing tribal knowledge or scattered documentation can be migrated to the new standardized format without losing correctness
- **Rollback scenarios** -- Test that downgrading to a previous inference server version correctly displays configurations validated for that version

### 7.3 Performance/Scalability

**Not Applicable** -- This feature adds static metadata fields to model cards in a repository. There are no API calls, data processing pipelines, or runtime operations that would introduce performance or scalability concerns. The vLLM serve command performance itself is out of scope (per the strategy's explicit exclusion of "performance tuning parameters").

### 7.4 RBAC/Authorization

**Not Applicable** -- The feature involves publishing static metadata to the model-metadata-collection repository and displaying it in the model catalog. There are no API endpoints, resource operations, or role-based access control requirements specific to this feature. Access control for the model-metadata-collection repo and catalog UI are existing concerns outside the scope of this feature.

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Model-metadata-collection repo may not be the correct vehicle for this feature (acknowledged in strategy assumptions) | High | Medium | Validate with PSAP-2219 stakeholders before implementation; ensure alignment on repo structure and publishing process |
| Validated configs may become stale when vLLM parser behavior changes across versions | High | High | Establish a regression testing process for re-validating all published configs after vLLM version updates; add version metadata to each config; monitor vLLM release notes for parser-related changes |
| PSAP team may not have access to target hardware for validation before publication | High | Medium | Define clear ownership and SLA for hardware-based validation; establish fallback process if target hardware is unavailable; consider staging environment for pre-validation |
| No documented process exists yet for adding new models to the metadata collection | Medium | High | Create and validate the documented process as part of AC #4 before first publication; ensure process includes validation checklist and approval gates |
| Dependency on upstream vLLM parser availability (e.g., Granite 4.0h Tiny KeyError with upstream-documented "granite4" parser name) | High | Medium | Establish parser availability verification as part of validation process; document parser name discrepancies; coordinate with vLLM upstream on parser naming conventions |
| Dependency on PSAP team for hardware-based validation creates bottleneck | Medium | High | Align validation schedule with PSAP team's existing hardware test cycles; establish clear SLA and communication channel; consider staging environment for pre-validation smoke tests |
| Configurations may become stale without process for flagging outdated data | Medium | Medium | Implement version metadata in each configuration; add automated staleness checks in CI; document re-validation process for new inference server releases |
| Incorrect configurations could be published if validation is incomplete or skipped | High | Medium | Enforce mandatory end-to-end validation before publication (AC #2); implement automated validation gate in publishing workflow; maintain validation audit trail |
| Tool calling section may be displayed for models with partial or unvalidated data | Medium | Low | Implement strict display logic per AC #5 (only show section when fully validated); add automated check in publishing pipeline to prevent partial configs |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure

| Component | Specification | Source for TBD Resolution |
|-----------|--------------|--------------------------|
| OpenShift cluster | TBD — resolve via RHOAI 3.5 compatibility matrix (expected OCP 4.14+) | RHOAI 3.5 product documentation |
| RHOAI version | 3.5 (Tech Preview) | Strategy document |
| Red Hat AI Inference Server (vLLM) | TBD — resolve via RHOAI 3.5 component version matrix | RHOAI 3.5 release manifest |
| GPU nodes | Per-model hardware profile (see Section 3.1 GPU table); minimum 4x A100 80GB or equivalent for the largest model (Qwen3-235B-A22B-GPTQ-Int4) | PSAP hardware validation docs |
| Model storage | S3-compatible object storage (e.g., ODF/NooBaa, MinIO, AWS S3) or PVC with ReadOnlyMany access containing model weights for all 5 models | Cluster provisioning playbook |
| Network | Route or NodePort exposing vLLM inference endpoints for API access; no external internet required for test execution (models served locally) | Cluster networking configuration |
| model-metadata-collection repo | Read access for model card content validation | GitHub org access |

### 9.2 Configuration

- **vLLM serve arguments per model**: See Section 3.2.4 table for `--tool-call-parser`, `--reasoning-parser`, `--chat-template`, `--enable-auto-tool-choice` values (TBD pending PSAP validation)
- **Environment variables**:
  - `MODEL_PATH` — path to model weights on storage (S3 URI or PVC mount path)
  - `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` — if using S3-compatible storage
  - `INFERENCE_ENDPOINT` — base URL of the vLLM inference service (e.g., `https://<route-host>`)
- **ServingRuntime / InferenceService manifests**: YAML manifests with validated vLLM arguments injected as container args; one manifest per model under test. Example structure:

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
          - "--tool-call-parser=<validated-parser>"
          - "--reasoning-parser=<validated-parser>"
          - "--chat-template=<validated-template>"
          - "--enable-auto-tool-choice"
  ```

- **Model card content**: "Tool Calling Configuration" section from model-metadata-collection repo, containing the published vLLM serve command for each model

### 9.3 Test Tools

| Tool | Version | Purpose |
|------|---------|---------|
| `oc` (OpenShift CLI) | 4.14+ (matching cluster version) | Inspect pods, logs, CRs; deploy ServingRuntime/InferenceService; manage service accounts and RBAC |
| `curl` | any | Quick API smoke tests against `/v1/models` and `/v1/chat/completions` |
| Python `openai` SDK | >= 1.0 | Primary test client for tool calling requests and response validation (structured access to `tool_calls` array) |
| Python `requests` | >= 2.28 | Fallback HTTP client for raw request/response inspection |
| `jq` | >= 1.6 | Parse and validate JSON response structure from CLI |
| `oc logs` / OpenShift logging | n/a | Debug silent failures, parser errors, and model loading issues in vLLM pods |

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-META | 4 | 2 | 2 | 0 |
| TC-CFG | 5 | 5 | 0 | 0 |
| TC-E2E | 8 | 6 | 2 | 0 |
| TC-NEG | 5 | 0 | 5 | 0 |
| TC-PROC | 2 | 0 | 0 | 2 |
| **Total** | **24** | **13** | **9** | **2** |

### 10.2 Configuration/Endpoint Coverage

| Endpoint | Test Cases | Coverage |
|----------|------------|----------|
| Model card metadata file | TC-META-001, TC-META-002, TC-META-003, TC-META-004 | |
| `vllm serve` with `--tool-call-parser` | TC-CFG-001, TC-NEG-002 | |
| `vllm serve` with `--reasoning-parser` | TC-CFG-002 | |
| `vllm serve` with `--chat-template` | TC-CFG-003, TC-NEG-003 | |
| `vllm serve` with `--enable-auto-tool-choice` | TC-CFG-004, TC-NEG-004 | |
| Tool calling request API (`/v1/chat/completions`) | TC-CFG-004, TC-CFG-005, TC-E2E-001 through TC-E2E-008 | |
| Tool calling response format | TC-E2E-006, TC-E2E-007, TC-E2E-008 | |
| Tool response round-trip | TC-E2E-001 through TC-E2E-005 | |
| Model-metadata-collection repo process doc | TC-PROC-001, TC-PROC-002 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-22 | Initial test plan |
| 1.1.0 | 2026-04-22 | Test cases generated: 24 TCs across 5 categories |

---

**End of Test Plan**

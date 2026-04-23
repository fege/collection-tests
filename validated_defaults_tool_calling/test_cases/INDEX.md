# Test Case Index — Validated Defaults for Tool Calling Configuration

**Test Plan**: [TestPlan.md](../TestPlan.md)
**Strategy**: [RHAISTRAT-1473](https://redhat.atlassian.net/browse/RHAISTRAT-1473)

## Quick Stats

| Metric | Count |
|--------|-------|
| Total Test Cases | 31 |
| P0 (Critical) | 16 |
| P1 (High) | 12 |
| P2 (Medium) | 3 |

## TC-META — Model Card Metadata Validation

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-META-001](TC-META-001.md) | Verify ToolCallingConfig OpenAPI schema defines correct fields, types, and constraints | P0 |
| [TC-META-002](TC-META-002.md) | Verify servingConfig.toolCalling present in API response for validated model | P0 |
| [TC-META-003](TC-META-003.md) | Verify servingConfig absent from API response for model without validated config | P0 |
| [TC-META-004](TC-META-004.md) | Verify convertModelProperties() serializes servingConfig to serving_config JSON property | P0 |
| [TC-META-005](TC-META-005.md) | Verify mapDBModelToAPIModel() deserializes serving_config to typed ServingConfig | P0 |
| [TC-META-006](TC-META-006.md) | Verify model-metadata-collection pipeline outputs servingConfig and removes Markdown | P1 |
| [TC-META-007](TC-META-007.md) | Verify tasks array includes tool-calling when servingConfig.toolCalling is present | P1 |
| [TC-META-008](TC-META-008.md) | Verify backward compatibility — existing API consumers unaffected by optional servingConfig | P1 |

## TC-CFG — Configuration Correctness

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-CFG-001](TC-CFG-001.md) | Validate toolCallParser value is recognized by vLLM for each in-scope model | P0 |
| [TC-CFG-002](TC-CFG-002.md) | Validate chatTemplate path loads correctly for each in-scope model | P0 |
| [TC-CFG-003](TC-CFG-003.md) | Validate enableAutoToolChoice is set and functional | P0 |
| [TC-CFG-004](TC-CFG-004.md) | Validate requiredArgs contains valid CLI arguments | P2 |
| [TC-CFG-005](TC-CFG-005.md) | Verify complete vllm serve command from servingConfig fields can be executed without modification | P0 |

## TC-E2E — End-to-End Tool Calling Validation

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Full tool calling round-trip with Qwen/Qwen3-235B-A22B-GPTQ-Int4 | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Full tool calling round-trip with openai/gpt-oss-120b | P0 |
| [TC-E2E-003](TC-E2E-003.md) | Full tool calling round-trip with nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 | P0 |
| [TC-E2E-004](TC-E2E-004.md) | Full tool calling round-trip with mistralai/Mistral-Small-4-119B-2603 | P0 |
| [TC-E2E-005](TC-E2E-005.md) | Full tool calling round-trip with Llama-3.3-70B-Instruct | P0 |
| [TC-E2E-006](TC-E2E-006.md) | Response format matches OpenAI-compatible schema across all models | P0 |
| [TC-E2E-007](TC-E2E-007.md) | Multi-tool selection scenario | P1 |
| [TC-E2E-008](TC-E2E-008.md) | Tool calling with streaming enabled | P1 |
| [TC-E2E-009](TC-E2E-009.md) | Catalog API to vLLM deployment end-to-end | P0 |

## TC-NEG — Negative Testing

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-NEG-001](TC-NEG-001.md) | Model without servingConfig does not expose tool calling configuration via API | P1 |
| [TC-NEG-002](TC-NEG-002.md) | Wrong toolCallParser produces detectable failure | P1 |
| [TC-NEG-003](TC-NEG-003.md) | Missing chatTemplate produces detectable failure | P1 |
| [TC-NEG-004](TC-NEG-004.md) | Without --enable-auto-tool-choice, model does not auto-select tools | P1 |
| [TC-NEG-005](TC-NEG-005.md) | Partial servingConfig (toolCallParser present but chatTemplate missing) is not published | P1 |
| [TC-NEG-006](TC-NEG-006.md) | Malformed serving_config JSON in database handled gracefully by mapDBModelToAPIModel() | P1 |
| [TC-NEG-007](TC-NEG-007.md) | tasks array with tool-calling but no servingConfig.toolCalling detected as data inconsistency | P1 |

## TC-PROC — Process Documentation Validation

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-PROC-001](TC-PROC-001.md) | Documented process exists for adding new tool calling configs to model-metadata-collection | P2 |
| [TC-PROC-002](TC-PROC-002.md) | Follow documented process to add a new model's tool calling config end-to-end | P2 |

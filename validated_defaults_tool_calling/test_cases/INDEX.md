# Test Case Index — Validated Defaults for Tool Calling Configuration

**Test Plan**: [TestPlan.md](../TestPlan.md)
**Strategy**: [RHAISTRAT-1473](https://redhat.atlassian.net/browse/RHAISTRAT-1473)

## Quick Stats

| Metric | Count |
|--------|-------|
| Total Test Cases | 24 |
| P0 (Critical) | 12 |
| P1 (High) | 8 |
| P2 (Medium) | 4 |

## TC-META — Model Card Metadata Validation

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-META-001](TC-META-001.md) | Verify "Tool Calling Configuration" section exists in model card for each in-scope model | P0 |
| [TC-META-002](TC-META-002.md) | Verify all four required CLI flags are present in model card configuration | P0 |
| [TC-META-003](TC-META-003.md) | Verify model card configuration is tied to a specific inference server version | P1 |
| [TC-META-004](TC-META-004.md) | Verify model card configuration contains a complete copy-paste-ready vllm serve command | P1 |

## TC-CFG — Configuration Correctness

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-CFG-001](TC-CFG-001.md) | Validate --tool-call-parser value is recognized by vLLM for each in-scope model | P0 |
| [TC-CFG-002](TC-CFG-002.md) | Validate --reasoning-parser value is recognized by vLLM for each in-scope model | P0 |
| [TC-CFG-003](TC-CFG-003.md) | Validate --chat-template value loads correctly for each in-scope model | P0 |
| [TC-CFG-004](TC-CFG-004.md) | Validate --enable-auto-tool-choice flag is set and functional for each in-scope model | P0 |
| [TC-CFG-005](TC-CFG-005.md) | Verify documented vllm serve command can be copy-pasted and executed without modification | P0 |

## TC-E2E — End-to-End Tool Calling Validation

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Full tool calling round-trip with Qwen/Qwen3-235B-A22B-GPTQ-Int4 | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Full tool calling round-trip with openai/gpt-oss-120b | P0 |
| [TC-E2E-003](TC-E2E-003.md) | Full tool calling round-trip with nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 | P0 |
| [TC-E2E-004](TC-E2E-004.md) | Full tool calling round-trip with mistralai/Mistral-Small-4-119B-2603 | P0 |
| [TC-E2E-005](TC-E2E-005.md) | Full tool calling round-trip with Llama-3.3-70B-Instruct | P0 |
| [TC-E2E-006](TC-E2E-006.md) | Verify tool calling response format matches OpenAI-compatible schema across all models | P0 |
| [TC-E2E-007](TC-E2E-007.md) | Verify multi-tool selection scenario | P1 |
| [TC-E2E-008](TC-E2E-008.md) | Verify tool calling round-trip with streaming enabled | P1 |

## TC-NEG — Negative Testing

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-NEG-001](TC-NEG-001.md) | Verify model card without validated config does not display "Tool Calling Configuration" section | P1 |
| [TC-NEG-002](TC-NEG-002.md) | Verify serving a model with wrong --tool-call-parser produces a detectable failure | P1 |
| [TC-NEG-003](TC-NEG-003.md) | Verify serving a model with missing --chat-template produces a detectable failure | P1 |
| [TC-NEG-004](TC-NEG-004.md) | Verify serving a model without --enable-auto-tool-choice does not auto-select tools | P1 |
| [TC-NEG-005](TC-NEG-005.md) | Verify model card with partial configuration is not published | P1 |

## TC-PROC — Process Documentation Validation

| Test Case ID | Title | Priority |
|-------------|-------|----------|
| [TC-PROC-001](TC-PROC-001.md) | Verify model-metadata-collection repo contains documented process for adding new configs | P2 |
| [TC-PROC-002](TC-PROC-002.md) | Follow the documented process to add a new model's tool calling config | P2 |

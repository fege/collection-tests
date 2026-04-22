---
test_case_id: TC-META-001
source_key: RHAISTRAT-1473
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-META-001: Verify "Tool Calling Configuration" section exists in model card for each in-scope model

**Objective**: Confirm that every model in the initial scope has a "Tool Calling Configuration" section present in its model card within the model-metadata-collection repository.

**Test Steps**:
1. For each model in the initial scope (Qwen/Qwen3-235B-A22B-GPTQ-Int4, openai/gpt-oss-120b, nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4, mistralai/Mistral-Small-4-119B-2603, Llama-3.3-70B-Instruct), locate the model card file in the model-metadata-collection repository
2. Search each model card for a section titled "Tool Calling Configuration" (or equivalent heading)
3. Verify that the section is present and non-empty

**Expected Results**:
- Each of the 5 in-scope models has a "Tool Calling Configuration" section in its model card
- The section contains at least the four required vLLM serve arguments (`--tool-call-parser`, `--reasoning-parser`, `--chat-template`, `--enable-auto-tool-choice`)
- No model card in scope has a missing or empty tool calling section

**Notes**: To be filled later in the process.

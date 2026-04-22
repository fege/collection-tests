---
feature: validated_defaults_tool_calling
source_key: RHAISTRAT-1473
score: 10
pass: true
verdict: Ready
scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 2
  consistency: 2
last_updated: '2026-04-22'
auto_revised: false
before_score: 9
before_scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 1
  consistency: 2
error: null
---
# Test Plan Review: validated_defaults_tool_calling

## Rubric Scores

| Criterion | Score | Evidence | Notes |
|-----------|-------|----------|-------|
| Specificity | 2 | Priorities reference feature-specific scenarios: P0 names end-to-end validation of tool calling configs. Risks name PSAP hardware access, model-metadata-collection repo, vLLM parser naming discrepancies (Granite 4.0h KeyError). | No boilerplate. Every priority and risk tied to this feature. |
| Grounding | 2 | All five models match strategy. Four CLI flags match strategy. Out-of-scope items mirror strategy. TBDs have resolution sources. | No fabricated details. All TBDs have named resolution sources. |
| Scope Fidelity | 2 | Every strategy AC maps to test objectives. No scope creep. Out-of-scope items absent from endpoints. | No orphans in either direction. |
| Actionability | 2 | Per-model GPU table, sample JSON request/response, tool response round-trip with validation criteria, detailed test users with service accounts and RBAC commands, specific tool versions. All TBDs have resolution sources. | Significant improvement from prior score of 1. A tester could begin environment setup immediately. |
| Consistency | 2 | All 6 cross-checks pass. Section 10.2 lists all 9 Section 4 endpoints. Test levels match interface types. Priorities match definitions. NFR categories consistent. Section 6 placeholder acceptable. | No contradictions. Revisions did not introduce inconsistencies. |

**Total: 10/10**

## Grounding Cross-Reference

| Section 4 Entry | Source Match | Status |
|-----------------|-------------|--------|
| Model card metadata file | Strategy AC #1: "Each model in the initial scope has a 'Tool Calling Configuration' section in its model card with the complete vllm serve arguments" | Grounded |
| `vllm serve` with `--tool-call-parser` | Strategy Summary: "validated --tool-call-parser, --reasoning-parser, --chat-template, and --enable-auto-tool-choice arguments directly in the model card" | Grounded |
| `vllm serve` with `--reasoning-parser` | Strategy Summary: same sentence as above | Grounded |
| `vllm serve` with `--chat-template` | Strategy Summary: same sentence as above | Grounded |
| `vllm serve` with `--enable-auto-tool-choice` | Strategy Summary: same sentence as above | Grounded |
| Tool calling request API (`/v1/chat/completions`) | Inferred from "tool call request" in What "Validated" Means and vLLM/OpenAI-compatible context | Extrapolated |
| Tool calling response format | Strategy: "correct tool_calls output"; Problem: "empty tool_calls arrays, parsing errors" | Grounded |
| Tool response round-trip | Strategy: "tool call request -> correct tool_calls output -> tool response round-trip" | Grounded |
| Model-metadata-collection repo process doc | Strategy AC #4 | Grounded |

## Consistency Cross-Checks

- Section 4 vs Section 1.2 scope: PASS
- Section 2.1 test levels vs Section 4 interface types: PASS
- Section 4 priorities vs Section 2.3 definitions: PASS
- Section 10.2 vs Section 4 endpoints: PASS
- Section 7 NFR categories vs feature scope: PASS
- Section 6.2 E2E coverage vs Section 4 P0 endpoints: PASS (pre-create-cases placeholder)

## Section-by-Section Feedback

All criteria scored 2 after revision — no further improvements needed.

## Revision History

| Pass | Score | Verdict | Notes |
|------|-------|---------|-------|
| 1 | 9/10 | Ready | Actionability at 1 due to TBD environment values and missing sample payloads. All other criteria at 2. |

### Cycle 1 Revision
- **Specificity**: N/A — scored 2
- **Grounding**: N/A — scored 2
- **Scope Fidelity**: N/A — scored 2
- **Actionability**: Rewrote Sections 3.1, 3.2, 3.3, and 9 to address all five gaps. Section 3.1: added per-model GPU hardware profile table with GPU type, count, and VRAM; pinned TBD items (OpenShift version, vLLM version) with the specific document type that resolves them. Section 3.2: added canonical sample tool calling request JSON, expected tool_calls response JSON with explicit validation criteria, full round-trip request JSON, per-model configuration argument table, and negative test data list. Section 3.3: replaced generic role descriptions with a table specifying service account names, required permissions, and setup commands. Section 9: restructured infrastructure as a specification table with TBD resolution sources, added environment variable names, added example ServingRuntime YAML manifest, and added a versioned test tools table.
- **Consistency**: N/A — scored 2

### Cycle 1 Revision
- **Actionability**: Sections 3.1, 3.2, 3.3, and 9 revised with per-model GPU requirements table, sample JSON request/response examples with validation criteria, tool response round-trip example, per-model configuration table, detailed test users table with service account names and RBAC setup commands, and specific tool versions. Score improved from 1 to 2.
- All other criteria: N/A — scored 2 on initial assessment.


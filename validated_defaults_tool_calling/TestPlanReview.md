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
last_updated: '2026-04-23'
auto_revised: true
before_score: 9
before_scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 2
  consistency: 1
error: null
---
# Test Plan Review: validated_defaults_tool_calling

## Rubric Scores

| Criterion | Score | Evidence | Notes |
|-----------|-------|----------|-------|
| Specificity | 2 | P0 definitions reference `ServingConfig`/`ToolCallingConfig` OpenAPI schema, `convertModelProperties()` serialization, `mapDBModelToAPIModel()` deserialization, end-to-end tool calling on target hardware. Risks name vLLM parser version drift, PSAP hardware bottleneck, `tasks`/`servingConfig` consistency, Granite 4.0h KeyError, JSON serialization silent data loss, Dashboard team dependency. | Swap test passes: "Dependency on upstream vLLM parser availability (e.g., Granite 4.0h Tiny KeyError with upstream-documented 'granite4' parser name)" is unique to this feature. Priority definitions reference feature-specific Go functions and schemas. |
| Grounding | 2 | All Section 4 entries trace to strategy or ADR. No fabrications detected. TBDs have named resolution sources (PSAP validation results, RHOAI 3.5 release manifest). vLLM 0.20.0 qualified as "per ADR `validatedOn` example -- confirm via release manifest." | Granite-4.0-H-Small reference example correctly attributed as ADR example (not in initial 5-model scope). Configuration TBDs for initial models correctly defer to PSAP team. |
| Scope Fidelity | 2 | Every strategy AC maps to test objectives: AC#1→Obj 4,7; AC#2→Obj 8; AC#3→Obj 3,8; AC#4→TC-PROC; AC#5→Obj 5. Out-of-scope items absent: runtime auto-detection, performance tuning. Dashboard UI correctly scoped out with note that QE validates API contract only. ADR additions (schema, serialization, API contract) correctly incorporated as in-scope. | No orphans in either direction. No scope creep. |
| Actionability | 2 | Per-model GPU table with specific GPU types and VRAM. Canonical JSON request/response with 4 validation criteria. 4 test user roles with named service accounts, RBAC bindings, and `oc` setup commands. Versioned tools (Python 3.11+, openai >= 1.0, Go 1.21+, oc 4.14+). YAML catalog input examples, API response JSON, database property format from ADR. ServingRuntime YAML template in Section 9.2. | A tester could begin environment setup immediately. TBDs are legitimate unknowns (PSAP validation not complete) with named resolution documents. |
| Consistency | 2 | All 6 cross-checks pass after revision. Dashboard UI removed from Section 4 (aligned with Section 1.2 Out of Scope). Section 10.2 lists all Section 4 endpoints. Priority assignments match definitions. Test levels match interface types. NFR categories appropriate. Section 6 E2E coverage addresses runtime P0 items. | Revision fixed Dashboard UI scope contradiction. Section 10.2 entries marked "(new TCs needed)" are acknowledged and will be addressed by `/test-plan.create-cases`. |

**Total: 10/10**

## Grounding Cross-Reference

| Section 4 Entry | Source Match | Status |
|-----------------|-------------|--------|
| `ServingConfig` / `ToolCallingConfig` in `api/openapi/src/catalog.yaml` | ADR: OpenAPI schema definitions for `ServingConfig` and `ToolCallingConfig` added to `api/openapi/src/catalog.yaml` | Grounded |
| `convertModelProperties()` in `yaml_catalog.go` | ADR: "In yaml_catalog.go, the convertModelProperties() function will be extended to handle servingConfig" | Grounded |
| `mapDBModelToAPIModel()` in `db_catalog.go` | ADR: "In db_catalog.go, the mapDBModelToAPIModel() function will deserialize it back" | Grounded |
| GET `/api/catalog/models/{modelId}` | ADR: API response format with `servingConfig.toolCalling` | Grounded |
| `serving_config` property in Properties table | ADR: "The servingConfig object is serialized as a JSON string and stored as a single property row" | Grounded |
| model-metadata-collection YAML output | Strategy: "model-metadata-collection repo"; ADR: "Output servingConfig in metadata.yaml" | Grounded |
| `servingConfig.toolCalling.supported` | ADR: `ToolCallingConfig` schema, `required: [supported]` | Grounded |
| `servingConfig.toolCalling.toolCallParser` | Strategy: "--tool-call-parser"; ADR: `toolCallParser` field | Grounded |
| `servingConfig.toolCalling.chatTemplate` | Strategy: "--chat-template"; ADR: `chatTemplate` field | Grounded |
| Tool calling request API (`/v1/chat/completions`) | Strategy: "tool call request → correct tool_calls output → tool response round-trip" | Grounded |
| Tool calling response format | Strategy: "correct tool_calls output"; Problem: "empty tool_calls arrays, parsing errors" | Grounded |
| GET `/api/catalog/models` | ADR: API response format (catalog list endpoint) | Extrapolated |
| `filterQuery` with `tasks=tool-calling` | ADR: "tasks is the filter, servingConfig is the payload" | Grounded |
| `servingConfig.toolCalling.enableAutoToolChoice` | Strategy: "--enable-auto-tool-choice"; ADR: `enableAutoToolChoice` field | Grounded |
| model-metadata-collection README rendering | ADR: "Remove tool-calling Markdown section from README" | Grounded |
| `tasks` array includes `tool-calling` | ADR: "Models that have servingConfig.toolCalling entry should also include tool-calling in their tasks array" | Grounded |
| Tool response round-trip | Strategy: "tool call request → correct tool_calls output → tool response round-trip" | Grounded |
| `servingConfig.toolCalling.requiredArgs` | ADR: `requiredArgs` array field in `ToolCallingConfig` schema | Grounded |
| Model-metadata-collection process documentation | Strategy AC #4: "documented process for adding validated tool calling configs for new models" | Grounded |

## Consistency Cross-Checks

- Section 4 vs Section 1.2 scope: PASS (Dashboard UI removed from Section 4, aligned with Out of Scope)
- Section 2.1 test levels vs Section 4 interface types: PASS
- Section 4 priorities vs Section 2.3 definitions: PASS
- Section 10.2 vs Section 4 endpoints: PASS (all entries listed, "(new TCs needed)" acknowledged)
- Section 7 NFR categories vs feature scope: PASS
- Section 6.2 E2E coverage vs Section 4 P0 endpoints: PASS (runtime P0 items covered; schema/serialization P0 items are unit/integration level, not E2E)

## Revision History

| Pass | Score | Verdict | Notes |
|------|-------|---------|-------|
| 1 (v1.0.0) | 10/10 | Ready | All criteria at 2. Initial plan from strategy only. |
| 2 (v2.0.0) | 9/10 | Ready | After ADR update. Consistency at 1 due to Dashboard UI scope contradiction. |

### v2.0.0 Revision
- **Consistency**: Removed Dashboard Tool Calling Configuration UI from Section 4 (was P1) — it contradicted Section 1.2 Out of Scope which states "Dashboard UI rendering of `servingConfig.toolCalling` (Dashboard team responsibility; this plan validates API contract only)." Also clarified UI Testing in Section 2.1 as Dashboard team responsibility. Score improved from 1 to 2.
- All other criteria: N/A — scored 2 on initial v2.0.0 assessment.

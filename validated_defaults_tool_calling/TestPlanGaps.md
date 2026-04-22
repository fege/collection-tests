---
feature: validated_defaults_tool_calling
source_key: RHAISTRAT-1473
status: Open
gap_count: 14
last_updated: '2026-04-22'
---
# Gaps -- Validated Defaults for Tool Calling Configuration

## Scope & Endpoints
- No specification of model card schema or file format -- would be resolved by: **ADR or technical design doc defining model card structure**
- No API specification for tool calling request/response format -- would be resolved by: **API spec defining request payloads, tool_calls output schema, and error responses**
- No definition of "target hardware profile" for each model -- would be resolved by: **Design doc or infrastructure specification mapping models to validated hardware configurations**
- No sample tool calling scenarios or expected outputs -- would be resolved by: **Test case specification defining representative tool definitions and expected behavior**
- No error handling requirements (e.g., what happens if a user deploys with wrong vLLM version, missing parser, incorrect hardware) -- would be resolved by: **Feature refinement or ADR defining error states and user-facing messages**
- No specification of the documented process for adding new configs (what repo, what format, who reviews, what validation gates) -- would be resolved by: **ADR defining contribution workflow and validation gates**

## Test Strategy & Risks
- ~~Validation checklist and acceptance criteria for "what validated means"~~ -- **PARTIALLY RESOLVED** by feature refinement (end-to-end on target hardware, tied to specific inference server version). **Still missing**: Concrete validation checklist with pass/fail criteria (e.g., what specific test scenarios, what constitutes a "correct" tool_calls output, error handling requirements).
- Model-metadata-collection schema and publishing process are not defined -- would be resolved by: **Design doc or ADR describing the repository structure, metadata format (YAML fields, file naming), publishing workflow, and integration points with the model catalog UI**
- ~~Version-specific parser behavior tracking~~ -- **RESOLVED** by feature refinement (configurations tied to specific Red Hat AI Inference Server versions, re-validation scoped to new releases).
- Target hardware profiles for validation are not enumerated -- would be resolved by: **Feature refinement or design doc listing the specific hardware configurations (GPU types, VRAM, node count, CUDA versions, etc.) that must be used for validation before publication**
- ~~Disconnected environment requirements~~ -- **RESOLVED** by feature refinement (metadata delivered in catalog container image, no additional connectivity).
- No process for flagging stale configurations when new inference server versions are released -- would be resolved by: **Design doc or CI/CD specification describing automated staleness checks and re-validation triggers**

## Environment & Infrastructure
- Target OpenShift and vLLM operator versions unspecified -- would be resolved by: **Feature refinement or design doc**
- Hardware profile requirements not defined (GPU type, VRAM, node count) -- would be resolved by: **Design doc or ADR**
- Model storage backend (S3 vs PVC) and access configuration not specified -- would be resolved by: **Design doc**
- Exact format and location of model cards in model-metadata-collection repo not defined -- would be resolved by: **Design doc or API spec**
- No definition of "silent failure" scenarios for negative testing -- would be resolved by: **Feature refinement or design doc**
- Tool call validation criteria (e.g., expected JSON schema for tool_calls output) not specified -- would be resolved by: **API spec or feature refinement**
- Process for accessing validated configs (UI, API, direct repo access) not defined -- would be resolved by: **Design doc or feature refinement**

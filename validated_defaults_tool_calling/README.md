# Validated Defaults for Tool Calling Configuration

Validated vLLM tool calling configurations published in model cards so users can copy-paste a known-good `vllm serve` command for supported models.

## Links

- **Strategy**: [RHAISTRAT-1473](https://redhat.atlassian.net/browse/RHAISTRAT-1473)
- **Related RFE**: [RHAIRFE-1428](https://redhat.atlassian.net/browse/RHAIRFE-1428) — Validated Defaults for Tool Calling Configuration (Stage 1)
- **Parent Outcome**: [RHAISTRAT-1357](https://redhat.atlassian.net/browse/RHAISTRAT-1357) — Make Red Hat Inference Server the Best for Agents

## Test Plan

- [TestPlan.md](TestPlan.md) — Full test plan for the feature

## Test Cases

- [test_cases/INDEX.md](test_cases/INDEX.md) — Complete test case index
- **24 test cases**: 13x P0, 9x P1, 2x P2
- Categories: META (metadata validation), CFG (configuration correctness), E2E (end-to-end round-trips), NEG (negative testing), PROC (process documentation)

## Test Implementation

Automated tests will be implemented in the appropriate downstream E2E test repository, covering model card metadata validation and end-to-end tool calling round-trips on target hardware.

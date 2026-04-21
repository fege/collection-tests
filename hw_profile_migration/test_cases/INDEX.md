# Test Cases -- Hardware Profile Migration Fix

**Source**: [RHOAIENG-48676](https://issues.redhat.com/browse/RHOAIENG-48676)
**Test Plan**: [../TestPlan.md](../TestPlan.md)

## Quick Stats

| Metric | Value |
|--------|-------|
| Total Test Cases | 18 |
| P0 (Critical) | 8 |
| P1 (High) | 6 |
| P2 (Medium) | 4 |

## Migration Logic (TC-MIG)

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-MIG-001](TC-MIG-001.md) | Migration from accelerator profile adds both hardware profile annotations | P0 |
| [TC-MIG-002](TC-MIG-002.md) | Namespace annotation points to the hardware profile's actual namespace | P0 |
| [TC-MIG-003](TC-MIG-003.md) | Migration creates hardware profile in global namespace from accelerator profile | P0 |
| [TC-MIG-004](TC-MIG-004.md) | Migration handles missing accelerator profile gracefully | P2 |
| [TC-MIG-005](TC-MIG-005.md) | Migration skips notebooks without accelerator profile references | P2 |

## Webhook Validation (TC-WH)

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-WH-001](TC-WH-001.md) | Webhook accepts notebook update with both name and namespace annotations | P0 |
| [TC-WH-002](TC-WH-002.md) | Webhook looks up hardware profile in annotated namespace, not notebook namespace | P0 |
| [TC-WH-003](TC-WH-003.md) | Webhook handles notebook with name annotation but missing namespace annotation | P0 |
| [TC-WH-004](TC-WH-004.md) | Webhook handles non-existent hardware profile referenced by annotations | P1 |

## Operator Stability (TC-OP)

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-OP-001](TC-OP-001.md) | Operator does not crashloop after migration from accelerator profiles | P0 |
| [TC-OP-002](TC-OP-002.md) | Operator reconciliation continues normally after migration completes | P0 |
| [TC-OP-003](TC-OP-003.md) | Operator emits event when hardware profile not found during migration | P1 |
| [TC-OP-004](TC-OP-004.md) | Migration failure for one notebook does not block others | P2 |

## Regression (TC-REG)

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-REG-001](TC-REG-001.md) | Container-size to hardware profile migration path remains functional | P1 |
| [TC-REG-002](TC-REG-002.md) | Notebooks without any profile are unaffected by migration | P1 |
| [TC-REG-003](TC-REG-003.md) | Workbenches can be created after migration completes | P1 |
| [TC-REG-004](TC-REG-004.md) | Mixed migration scenario with both accelerator and container-size profiles | P2 |

## End-to-End Scenarios (TC-E2E)

| Test Case ID | Title | Priority |
|--------------|-------|----------|
| [TC-E2E-001](TC-E2E-001.md) | Full migration lifecycle from accelerator profile to hardware profile | P0 |
| [TC-E2E-002](TC-E2E-002.md) | Webhook validation after migration -- migrated notebook can be updated | P0 |
| [TC-E2E-003](TC-E2E-003.md) | Multiple notebooks with different accelerator profiles all migrate correctly | P1 |

---
feature: hw_profile_migration
source_key: RHOAIENG-48676
source_type: issue
version: 1.0.0
status: In Review
author: AI Core Platform (Crucible)
additional_docs: []
last_updated: '2026-04-21'
reviewers: []
---
# Hardware Profile Migration Test Plan
**AI Core Platform (Crucible) -- Hardware Profile Migration Bug Fix Validation**

**Strategy**: [RHOAIENG-48676](https://issues.redhat.com/browse/RHOAIENG-48676)

---

## 1. Executive Summary

### 1.1 Purpose
This test plan validates the bug fix for RHOAIENG-48676, a Blocker-severity defect in the RHOAI 3.3 hardware profile migration path. When notebooks using accelerator profiles are migrated to hardware profiles during an operator upgrade, the migration logic fails to add the `opendatahub.io/hardware-profile-namespace` annotation when the hardware profile originates from an accelerator profile. This missing annotation causes the mutating webhook to look up the hardware profile in the notebook's namespace instead of the global namespace (`redhat-ods-applications`), resulting in a webhook rejection, failed notebook annotation, and operator crashloop.

The fix ensures that the migration logic at `upgrade_utils.go#L672` adds both the `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations for accelerator-profile-originated hardware profiles, matching the behavior already in place for container-size-originated hardware profiles.

### 1.2 Scope

#### In Scope (AI Core Platform Responsibilities)
- Validation that migration logic adds `opendatahub.io/hardware-profile-namespace` annotation when migrating notebooks from accelerator profiles
- Validation that migration logic adds `opendatahub.io/hardware-profile-name` annotation when migrating notebooks from accelerator profiles
- Verification that webhook correctly locates hardware profiles in the global namespace when both annotations are present
- Verification that notebooks are successfully annotated after migration
- Verification that operator does not enter crashloop after migration
- Unit testing of the migration logic fix
- Cypress mocked testing of the migration flow

#### Out of Scope (Other Teams)
- Migration from container sizes to hardware profiles (existing functionality, not affected by this bug -- covered by regression only)
- Hardware profile creation logic (unrelated to the annotation bug)
- Accelerator profile creation logic (unrelated to the annotation bug)
- Notebook creation outside of migration context
- Dashboard UI linking between notebooks and hardware profiles (symptom, not root cause)

### 1.3 Test Objectives
1. Verify that migrating a notebook using an accelerator profile adds both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations
2. Verify that the namespace annotation points to the correct namespace where the hardware profile was created (global namespace `redhat-ods-applications`)
3. Verify that the webhook successfully validates notebook updates when both annotations are present
4. Verify that the webhook correctly locates hardware profiles in the namespace specified by the `opendatahub.io/hardware-profile-namespace` annotation
5. Verify that the migration completes without causing operator crashloop
6. Verify that the fix handles edge cases where accelerator profiles exist in non-global namespaces

---

## 2. Test Strategy

### 2.1 Test Levels
- **API Integration Testing** -- Kubernetes API interactions, notebook annotation updates, webhook validation
- **Data Validation Testing** -- Annotation persistence, hardware profile namespace resolution, migration state verification
- **Functional Testing** -- Migration logic from accelerator profiles to hardware profiles, webhook rejection/acceptance flows
- **Regression Testing** -- Ensure container-size-to-hardware-profile migration path remains intact, verify non-migrated notebooks are unaffected

### 2.2 Test Types
- **Positive Testing** -- Successful migration from accelerator profile to hardware profile with correct annotations, webhook accepts annotated notebooks
- **Negative Testing** -- Webhook rejects notebooks with missing namespace annotation, operator handles migration failures gracefully without crashloop
- **Boundary Testing** -- Multiple accelerator profiles in different namespaces, notebooks using both accelerator profiles and container sizes, global vs. namespaced hardware profiles
- **Regression Testing** -- Verify fix doesn't break existing container-size migration path, ensure notebooks without profiles remain functional

### 2.3 Test Priorities
- **P0 (Critical)** -- Migration adds both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations when migrating from accelerator profiles; webhook accepts the annotated notebook; operator does not crashloop
- **P1 (High)** -- Container-size migration path remains functional; notebooks with mixed profile types migrate correctly; dashboard displays correct hardware profile linkage
- **P2 (Medium)** -- Migration handles edge cases (missing profiles, orphaned notebooks); operator logs clear error messages for migration failures

---

## 3. Test Environment

### 3.1 Test Cluster Configuration
- **OpenShift cluster** with RHOAI operator installed
- **RHOAI version**: 3.3 (version containing hardware profile migration feature and the fix)
- **Operator**: opendatahub-operator with migration logic from `upgrade_utils.go`
- **Webhook**: Mutating webhook for notebook validation/mutation (`mutating.go`)
- **Global namespace**: `redhat-ods-applications` (where accelerator/hardware profiles are created)
- **User namespace**: At least one user project namespace for notebook creation (e.g., `anthony-test-project`)

### 3.2 Test Data Requirements
- **Accelerator Profile CR**: Sample accelerator profile (e.g., `anthony-test-accel-profile`) in `redhat-ods-applications` namespace
- **Notebook CR**: Sample notebook that references the accelerator profile (e.g., `anthony-test-wb` in `anthony-test-project`)
- **Expected Hardware Profile CR**: The migrated hardware profile (e.g., `anthony-test-accel-profile-notebooks`) in global namespace
- **Annotation fixtures**: Test data showing expected before/after annotation states:
  - Before: Notebook with accelerator profile reference
  - After: Notebook with both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations

### 3.3 Test Users
- **Cluster admin**: To install/upgrade operator and create resources in global namespace
- **Namespace admin**: To create notebooks in user project namespace
- **Service account**: Operator service account with permissions to update notebooks and create hardware profiles across namespaces

---

## 4. Methods Under Test

| Method/Component | Type | Purpose | Priority |
|------------------|------|---------|----------|
| `upgrade_utils.go#L672` (migration logic) | Go Method | Adds hardware profile annotations to notebooks during migration from accelerator profiles | P0 |
| `mutating.go#L237` (webhook logic) | Go Method | Validates notebook updates by looking up hardware profiles using namespace annotation | P0 |
| Notebook resource annotations | Kubernetes Resource | Stores `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations | P0 |
| Operator reconciliation loop | Go Method | Processes notebook migrations and handles errors without entering crashloop | P0 |
| Hardware profile creation in global namespace | Kubernetes Resource | Creates hardware profile in `redhat-ods-applications` namespace during migration | P1 |

---

## 5. Test Cases

**18 test cases** generated across 5 categories. See the full index for details.

**Test Cases Directory**: [test_cases/](test_cases/)
**Complete Test Case Index**: [test_cases/INDEX.md](test_cases/INDEX.md)

### 5.1 Test Case Organization

| Category | Test Cases | Priority Distribution |
|----------|------------|----------------------|
| TC-MIG (Migration Logic) | 5 | 3x P0, 2x P2 |
| TC-WH (Webhook Validation) | 4 | 3x P0, 1x P1 |
| TC-OP (Operator Stability) | 4 | 2x P0, 1x P1, 1x P2 |
| TC-REG (Regression) | 4 | 3x P1, 1x P2 |
| TC-E2E (End-to-End) | 3 | 2x P0, 1x P1 |

### 5.2 Test Case Naming Convention

Test cases follow the naming pattern: `TC-<CATEGORY>-<NUMBER>`

- `TC-MIG`: Migration logic test cases (annotation addition, namespace resolution)
- `TC-WH`: Webhook validation test cases (acceptance/rejection flows)
- `TC-OP`: Operator stability test cases (crashloop prevention, reconciliation)
- `TC-REG`: Regression test cases (container-size migration, unaffected notebooks)
- `TC-E2E`: End-to-end migration scenarios

---

## 6. E2E Test Scenarios

End-to-end scenarios that validate the user journeys defined in the strategy. Each scenario maps to one or more TC-E2E-*.md test cases generated by `/test-plan.create-cases`.

> **Requirement**: At least one E2E scenario MUST be generated for each P0 endpoint in Section 4.
> E2E scenarios will be filled by `/test-plan.create-cases`.

### 6.1 Scenario Summary

| ID | Scenario | Endpoints Covered | Priority |
|----|----------|-------------------|----------|
| TC-E2E-001 | Full migration lifecycle from accelerator profile to hardware profile | Migration logic, Notebook annotations, HW profile creation, Operator reconciliation | P0 |
| TC-E2E-002 | Webhook validation after migration -- migrated notebook can be updated | Webhook logic, Notebook annotations | P0 |
| TC-E2E-003 | Multiple notebooks with different accelerator profiles all migrate correctly | Migration logic, Notebook annotations, HW profile creation, Operator reconciliation | P1 |

### 6.2 E2E Coverage Matrix

| Endpoint (from Section 4) | E2E Scenarios |
|----------------------------|---------------|
| `upgrade_utils.go#L672` (migration logic) | TC-E2E-001, TC-E2E-003 |
| `mutating.go#L237` (webhook logic) | TC-E2E-001, TC-E2E-002 |
| Notebook resource annotations | TC-E2E-001, TC-E2E-002, TC-E2E-003 |
| Operator reconciliation loop | TC-E2E-001, TC-E2E-003 |
| Hardware profile creation in global namespace | TC-E2E-001, TC-E2E-003 |

---

## 7. Non-Functional Requirements

Each category below must be explicitly addressed. If a category does not apply to this feature, state **Not Applicable** with a brief justification.

### 7.1 Disconnected/Air-Gapped

**Not Applicable** -- This is a migration bug fix for in-cluster resource annotations. No external registries, image pulls, or network-dependent catalog sources are involved. The hardware profile and notebook resources are Kubernetes CRDs managed entirely by the operator within the cluster.

### 7.2 Upgrade/Migration

**Critical** -- This bug is specifically an upgrade/migration defect:
- **Backwards Compatibility**: Verify notebooks using accelerator profiles (pre-3.3) migrate correctly to hardware profiles (3.3+)
- **Migration Path Testing**: Test upgrade from RHOAI versions that use accelerator profiles to 3.3 with the fix
- **Rollback Scenarios**: Verify operator recovers if migration is interrupted; test idempotency if upgrade is re-attempted
- **CRD Schema Changes**: Validate annotation schema (`opendatahub.io/hardware-profile-name` + `opendatahub.io/hardware-profile-namespace`) is consistent with webhook expectations
- **State Persistence**: Ensure migrated notebooks retain correct annotations after operator restart
- **Multi-Stage Migration**: Test scenarios with partial migration completion (some notebooks migrated, some not)

### 7.3 Performance/Scalability

**Medium Priority** -- Migration performance considerations:
- **Batch Migration**: Test migration of large numbers of notebooks (100+ notebooks using accelerator profiles)
- **Operator Restart Time**: Ensure operator doesn't crashloop indefinitely; verify exponential backoff or retry limits
- **Resource Consumption**: Monitor operator memory/CPU during migration execution
- **Migration Timeout**: Verify migration completes within acceptable timeframe for production upgrades

### 7.4 RBAC/Authorization

**Not Applicable** -- The bug is in operator-internal migration logic and webhook validation, not user-facing authorization. The operator runs with service account permissions to update notebook annotations; the webhook runs with cluster-scoped validation permissions. No RBAC boundary changes or multi-tenant authorization issues are introduced by this fix.

---

## 8. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Fix only addresses accelerator profile migration but breaks container-size migration path | High | Medium | Comprehensive regression tests covering both migration paths; verify upgrade_utils.go#L672 logic covers all branches |
| Webhook validation logic has additional undocumented requirements for annotation pairs | High | Low | Test webhook behavior directly with unit tests; review mutating.go#L237 for all annotation validation rules; add integration tests with various annotation combinations |
| Migration fails silently for notebooks in non-standard namespaces | Medium | Medium | Test migration with notebooks in user namespaces, global namespace, and edge-case namespaces; verify operator logs migration failures clearly |
| Existing notebooks with partial annotations (only `hardware-profile-name`, no `hardware-profile-namespace`) remain in broken state after fix | Medium | High | Test upgrade scenario where notebooks were partially annotated by broken version; verify reconciliation logic to backfill missing namespace annotations |
| Hardware profile created in non-global namespace causes webhook to fail namespace resolution | Medium | Low | Test scenarios where hardware profiles exist in multiple namespaces; verify webhook namespace resolution precedence |
| Operator crashloop recovery takes excessive time in production, impacting service availability | High | Low | Implement circuit breaker or retry limit in migration logic; add liveness probe tolerance for migration phase; document rollback procedure |

---

## 9. Test Environment Requirements

### 9.1 Infrastructure
- Single OpenShift cluster with operator lifecycle management
- Operator deployment: opendatahub-operator with upgrade/migration capabilities
- Mutating webhook: Active and configured to intercept notebook updates
- Multi-namespace setup: Global namespace (`redhat-ods-applications`) + user project namespace(s)

### 9.2 Configuration
- Operator subscription/CatalogSource: To trigger operator upgrade and migration
- Webhook configuration: MutatingWebhookConfiguration for notebook validation
- RBAC: Operator service account must have cross-namespace permissions (create hardware profiles in global namespace, update notebooks in user namespaces)
- Feature flags (if applicable): Migration enablement flags for hardware profile migration

### 9.3 Test Tools
- **kubectl/oc**: To create CRs, inspect annotations, check operator logs
- **jq/yq**: To parse and validate YAML/JSON for CRs and annotations
- **Operator logs viewer**: To verify migration logic execution and detect crashloop errors
- **Webhook logs viewer**: To trace webhook rejection messages
- **Cypress**: For mocked UI tests of migration flows

---

## 10. Appendix

### 10.1 Test Case Summary

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| TC-MIG (Migration Logic) | 5 | 3 | 0 | 2 |
| TC-WH (Webhook Validation) | 4 | 3 | 1 | 0 |
| TC-OP (Operator Stability) | 4 | 2 | 1 | 1 |
| TC-REG (Regression) | 4 | 0 | 3 | 1 |
| TC-E2E (End-to-End) | 3 | 2 | 1 | 0 |
| **Total** | **18** | **8** | **6** | **4** |

### 10.2 Method Coverage

| Method/Component | Test Cases | Coverage |
|------------------|------------|----------|
| `upgrade_utils.go#L672` (migration logic) | TC-MIG-001, TC-MIG-002, TC-MIG-003, TC-MIG-004, TC-MIG-005, TC-E2E-001, TC-E2E-003 | |
| `mutating.go#L237` (webhook logic) | TC-WH-001, TC-WH-002, TC-WH-003, TC-WH-004, TC-E2E-002 | |
| Notebook resource annotations | TC-MIG-001, TC-MIG-002, TC-WH-001, TC-WH-002, TC-E2E-001, TC-E2E-002, TC-E2E-003 | |
| Operator reconciliation loop | TC-OP-001, TC-OP-002, TC-OP-003, TC-OP-004, TC-E2E-001, TC-E2E-003 | |
| Hardware profile creation in global namespace | TC-MIG-003, TC-REG-001, TC-E2E-001, TC-E2E-003 | |

### 10.3 Document Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-04-21 | Initial test plan |

---

**End of Test Plan**

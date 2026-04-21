---
test_case_id: TC-REG-002
source_key: RHOAIENG-48676
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-REG-002: Notebooks without any profile are unaffected by migration

**Objective**: Verify that notebooks that do not use any accelerator profile or container size are left completely untouched by the migration process.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version)
- Notebook `regtest-notebook-plain` in namespace `regtest-project` with no accelerator profile or container size annotations

**Test Steps**:
1. Record the notebook's annotations before migration:
   ```bash
   oc get notebook regtest-notebook-plain -n regtest-project -o jsonpath='{.metadata.annotations}' > /tmp/before-annotations.json
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Wait for operator reconciliation to complete
4. Record the notebook's annotations after migration:
   ```bash
   oc get notebook regtest-notebook-plain -n regtest-project -o jsonpath='{.metadata.annotations}' > /tmp/after-annotations.json
   ```
5. Compare before and after annotations:
   ```bash
   diff /tmp/before-annotations.json /tmp/after-annotations.json
   ```

**Expected Results**:
- No `opendatahub.io/hardware-profile-name` or `opendatahub.io/hardware-profile-namespace` annotations are added
- The notebook's existing annotations remain unchanged
- No migration-related events are emitted for this notebook

**Notes**: To be filled later in the process.

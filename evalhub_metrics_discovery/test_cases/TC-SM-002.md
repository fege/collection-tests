---
test_case_id: TC-SM-002
source_key: RHAISTRAT-1507
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-27'
---
# TC-SM-002: ServiceMonitor carries ownerReference to EvalHub CR

**Objective**: Verify that the auto-created ServiceMonitor has an `ownerReference` pointing to the parent EvalHub CR, enabling Kubernetes garbage collection.

**Preconditions**:
- EvalHub CR deployed with metrics enabled (TC-SM-001 completed)
- ServiceMonitor exists in the same namespace

**Test Steps**:
1. Retrieve the ServiceMonitor resource and inspect its `metadata.ownerReferences` field
   ```bash
   oc get servicemonitor <name> -n opendatahub -o jsonpath='{.metadata.ownerReferences}'
   ```
2. Verify the ownerReference `kind` is `EvalHub`
3. Verify the ownerReference `name` matches the EvalHub CR name
4. Verify the ownerReference `uid` matches the EvalHub CR's UID
   ```bash
   oc get evalhub evalhub-test -n opendatahub -o jsonpath='{.metadata.uid}'
   ```

**Expected Results**:
- `metadata.ownerReferences` contains exactly one entry
- The entry has `kind: EvalHub`, `name: evalhub-test`, and the correct `uid`
- `blockOwnerDeletion` is set (enabling cascading delete)

**Notes**: To be filled later in the process.

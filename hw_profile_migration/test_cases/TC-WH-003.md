---
test_case_id: TC-WH-003
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-WH-003: Webhook handles notebook with name annotation but missing namespace annotation

**Objective**: Verify that the webhook handles a notebook update where `opendatahub.io/hardware-profile-name` is present but `opendatahub.io/hardware-profile-namespace` is missing — the exact scenario that triggered the original bug.

**Preconditions**:
- OpenShift cluster with RHOAI 3.3 operator installed (with the fix)
- Hardware profile `whtest-partial-profile` exists in `redhat-ods-applications` namespace
- Hardware profile `whtest-partial-profile` does NOT exist in `whtest-project` namespace
- Notebook `whtest-notebook-03` exists in `whtest-project` namespace

**Test Steps**:
1. Attempt to annotate the notebook with only the name annotation (no namespace):
   ```bash
   oc annotate notebook whtest-notebook-03 -n whtest-project \
     opendatahub.io/hardware-profile-name=whtest-partial-profile \
     --overwrite
   ```
2. Check the webhook behavior — it should either:
   - Default to the notebook namespace and fail to find the profile, OR
   - Emit an event and skip annotation without crashing
3. Check operator pod status:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator
   ```

**Expected Results**:
- The operator does NOT enter a crashloop regardless of the webhook outcome
- If the webhook rejects the update, it provides a clear error message
- If the webhook accepts the update, it emits an event indicating the hardware profile was not found in the notebook's namespace
- The operator remains in Running state with no excessive restarts

**Notes**: To be filled later in the process.

---
test_case_id: TC-WH-004
source_key: RHOAIENG-48676
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-WH-004: Webhook handles non-existent hardware profile referenced by annotations

**Objective**: Verify that the webhook handles a notebook update where both annotations are present but the referenced hardware profile does not exist in the specified namespace.

**Preconditions**:
- OpenShift cluster with RHOAI 3.3 operator installed
- No hardware profile named `whtest-nonexistent-profile` in any namespace
- Notebook `whtest-notebook-04` exists in `whtest-project` namespace

**Test Steps**:
1. Attempt to annotate the notebook referencing a non-existent hardware profile:
   ```bash
   oc annotate notebook whtest-notebook-04 -n whtest-project \
     opendatahub.io/hardware-profile-name=whtest-nonexistent-profile \
     opendatahub.io/hardware-profile-namespace=redhat-ods-applications \
     --overwrite
   ```
2. Check the webhook response
3. Check operator pod status:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator
   ```

**Expected Results**:
- The webhook emits an event indicating hardware profile not found
- The operator does NOT enter a crashloop
- The webhook behavior is predictable (either rejects with clear error or accepts and emits warning event)

**Notes**: To be filled later in the process.

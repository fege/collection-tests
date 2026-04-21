---
test_case_id: TC-WH-001
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-WH-001: Webhook accepts notebook update with both name and namespace annotations

**Objective**: Verify that the mutating webhook at `mutating.go#L237` accepts a notebook update when both `opendatahub.io/hardware-profile-name` and `opendatahub.io/hardware-profile-namespace` annotations are present and point to a valid hardware profile.

**Preconditions**:
- OpenShift cluster with RHOAI 3.3 operator installed (with the fix)
- Hardware profile `whtest-hw-profile` exists in `redhat-ods-applications` namespace
- Notebook `whtest-notebook-01` exists in `whtest-project` namespace

**Test Steps**:
1. Annotate the notebook with both hardware profile annotations:
   ```bash
   oc annotate notebook whtest-notebook-01 -n whtest-project \
     opendatahub.io/hardware-profile-name=whtest-hw-profile \
     opendatahub.io/hardware-profile-namespace=redhat-ods-applications \
     --overwrite
   ```
2. Verify the update was accepted (command exits with code 0)
3. Confirm the annotations are persisted:
   ```bash
   oc get notebook whtest-notebook-01 -n whtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-name}'
   oc get notebook whtest-notebook-01 -n whtest-project \
     -o jsonpath='{.metadata.annotations.opendatahub\.io/hardware-profile-namespace}'
   ```

**Expected Results**:
- The `oc annotate` command succeeds without webhook rejection
- `opendatahub.io/hardware-profile-name` is set to `whtest-hw-profile`
- `opendatahub.io/hardware-profile-namespace` is set to `redhat-ods-applications`
- No errors in the webhook logs related to this notebook update

**Notes**: To be filled later in the process.

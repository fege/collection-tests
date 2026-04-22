---
test_case_id: TC-WH-002
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-WH-002: Webhook looks up hardware profile in annotated namespace, not notebook namespace

**Objective**: Verify that the webhook uses the `opendatahub.io/hardware-profile-namespace` annotation to determine where to look up the hardware profile, rather than defaulting to the notebook's own namespace.

**Preconditions**:
- OpenShift cluster with RHOAI 3.3 operator installed
- Hardware profile `whtest-cross-ns-profile` exists in `redhat-ods-applications` namespace
- Hardware profile `whtest-cross-ns-profile` does NOT exist in `whtest-userproject` namespace
- Notebook `whtest-notebook-02` exists in `whtest-userproject` namespace

**Test Steps**:
1. Confirm the hardware profile exists only in the global namespace:
   ```bash
   oc get hardwareprofile whtest-cross-ns-profile -n redhat-ods-applications
   oc get hardwareprofile whtest-cross-ns-profile -n whtest-userproject 2>&1 | grep "not found"
   ```
2. Annotate the notebook with both annotations pointing to the global namespace:
   ```bash
   oc annotate notebook whtest-notebook-02 -n whtest-userproject \
     opendatahub.io/hardware-profile-name=whtest-cross-ns-profile \
     opendatahub.io/hardware-profile-namespace=redhat-ods-applications \
     --overwrite
   ```
3. Verify the update was accepted

**Expected Results**:
- The webhook accepts the update because it finds `whtest-cross-ns-profile` in `redhat-ods-applications` (the annotated namespace)
- The webhook does NOT reject the update by looking for the profile in `whtest-userproject` (the notebook's namespace)
- Both annotations are persisted on the notebook

**Notes**: To be filled later in the process.

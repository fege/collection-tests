---
test_case_id: TC-OP-002
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-OP-002: Operator reconciliation continues normally after migration completes

**Objective**: Verify that the operator reconciliation loop functions normally after the hardware profile migration completes — new notebook operations, operator restarts, and other reconciliation tasks work as expected.

**Preconditions**:
- OpenShift cluster with RHOAI 3.3 operator installed (migration already completed)
- At least one notebook successfully migrated from accelerator profile to hardware profile

**Test Steps**:
1. Verify operator is in Running state post-migration:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator
   ```
2. Create a new notebook in a user namespace to verify operator reconciliation:
   ```bash
   oc apply -f - <<EOF
   apiVersion: kubeflow.org/v1
   kind: Notebook
   metadata:
     name: optest-new-notebook
     namespace: optest-project
   spec:
     template:
       spec:
         containers:
         - name: notebook
           image: quay.io/opendatahub/notebooks:jupyter-minimal
   EOF
   ```
3. Verify the operator reconciles the new notebook without errors:
   ```bash
   oc logs -l name=rhods-operator -n redhat-ods-operator --tail=50 | grep "optest-new-notebook"
   ```
4. Delete the test notebook and verify cleanup:
   ```bash
   oc delete notebook optest-new-notebook -n optest-project
   ```

**Expected Results**:
- Operator remains in Running state throughout
- New notebook is created and reconciled successfully
- No migration-related errors appear in operator logs for the new notebook
- Operator handles lifecycle events normally

**Notes**: To be filled later in the process.

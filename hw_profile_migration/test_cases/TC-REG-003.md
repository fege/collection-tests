---
test_case_id: TC-REG-003
source_key: RHOAIENG-48676
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-REG-003: Workbenches can be created after migration completes

**Objective**: Verify that new workbenches (notebooks) can be created through the RHOAI dashboard after the hardware profile migration completes, confirming the operator and webhook are functioning normally.

**Preconditions**:
- OpenShift cluster with RHOAI 3.3 operator installed (migration completed successfully)
- At least one notebook previously migrated from accelerator profile to hardware profile
- Operator pod in Running state

**Test Steps**:
1. Verify operator is running and stable:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator
   ```
2. Create a new workbench via the RHOAI dashboard or CLI:
   ```bash
   oc apply -f - <<EOF
   apiVersion: kubeflow.org/v1
   kind: Notebook
   metadata:
     name: regtest-new-workbench
     namespace: regtest-project
   spec:
     template:
       spec:
         containers:
         - name: notebook
           image: quay.io/opendatahub/notebooks:jupyter-minimal
   EOF
   ```
3. Verify the notebook is created and reconciled:
   ```bash
   oc get notebook regtest-new-workbench -n regtest-project
   ```
4. Verify no webhook errors for the new notebook:
   ```bash
   oc logs -l name=rhods-operator -n redhat-ods-operator --tail=50 | grep "regtest-new-workbench"
   ```

**Expected Results**:
- New workbench is created successfully
- Webhook does not reject the new notebook
- Operator reconciles the new notebook without errors
- Existing migrated notebooks remain unaffected

**Notes**: To be filled later in the process.

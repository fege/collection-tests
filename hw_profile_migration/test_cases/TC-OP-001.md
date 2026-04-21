---
test_case_id: TC-OP-001
source_key: RHOAIENG-48676
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-21'
---
# TC-OP-001: Operator does not crashloop after migration from accelerator profiles

**Objective**: Verify that the RHOAI operator completes the hardware profile migration from accelerator profiles without entering a crashloop, specifically that the fix at `upgrade_utils.go#L672` prevents the webhook rejection cascade that caused 22+ hour crashloops.

**Preconditions**:
- OpenShift cluster with RHOAI operator installed (pre-upgrade version with accelerator profiles)
- At least one accelerator profile in `redhat-ods-applications` namespace
- At least one notebook using the accelerator profile in a user namespace
- Record operator pod restart count before migration

**Test Steps**:
1. Record baseline operator pod state:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator -o jsonpath='{.items[0].status.containerStatuses[0].restartCount}'
   ```
2. Trigger the hardware profile migration (operator upgrade to 3.3)
3. Monitor operator pod status for 10 minutes:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator -w
   ```
4. After migration settles, check final restart count:
   ```bash
   oc get pods -l name=rhods-operator -n redhat-ods-operator -o jsonpath='{.items[0].status.containerStatuses[0].restartCount}'
   ```
5. Check operator logs for migration completion:
   ```bash
   oc logs -l name=rhods-operator -n redhat-ods-operator --tail=200 | grep -i "migration\|hardware.profile"
   ```

**Expected Results**:
- Operator pod remains in Running state after migration
- Restart count does not increase by more than 1 (for the upgrade itself)
- No `failed to set HardwareProfile annotation` errors in operator logs
- Migration completion is logged

**Notes**: To be filled later in the process.

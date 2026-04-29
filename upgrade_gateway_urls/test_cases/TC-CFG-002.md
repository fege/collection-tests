---
test_case_id: TC-CFG-002
source_key: RHOAIENG-48747
priority: P0
status: Draft
automation_status: Not Started
last_updated: '2026-04-28'
upgrade_phase: both
---
# TC-CFG-002: Base notebook Service exposes port 80 with targetPort 8888

**Objective**: Confirm that the Kubernetes Service for an unmigrated base notebook container exposes port 80 with targetPort 8888, which is the expected port mapping that HTTPRoutes must target.

**Preconditions**:
- Code-server workbench `codeserver-wb` is running in namespace `upgrade-url-test`
- Workbench has not been migrated (no `inject-auth: true` annotation)

**Test Steps**:
1. Retrieve the Service port specification for the workbench:
   ```bash
   oc get service codeserver-wb -n upgrade-url-test \
     -o jsonpath='{range .spec.ports[*]}port={.port} targetPort={.targetPort}{"\n"}{end}'
   ```
2. Verify that port 80 is present with targetPort 8888.
3. Verify that port 8888 is not exposed as a direct service port:
   ```bash
   oc get service codeserver-wb -n upgrade-url-test \
     -o jsonpath='{.spec.ports[?(@.port==8888)].port}'
   ```

**Expected Results**:
- The Service has a port entry with `port: 80` and `targetPort: 8888`
- Step 3 returns empty output (port 8888 is not directly exposed as a service port)

**Notes**: To be filled later in the process.

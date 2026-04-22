---
test_case_id: TC-UPG-003
source_key: RHOAIENG-48787
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-UPG-003: OpenShift Route resource reflects new route pattern after upgrade

**Objective**: Verify that the OpenShift Route resources in the RHOAI namespace reflect the expected `data-science-gateway` naming pattern after upgrade from pre-3.3 to 3.3.

**Preconditions**:
- OpenShift cluster with RHOAI upgrade to 3.3 completed
- User with cluster-admin or project-admin role

**Test Steps**:
1. List all routes in the RHOAI namespace after upgrade:
   ```bash
   oc get routes -n redhat-ods-applications -o wide
   ```
2. Verify a route with the `data-science-gateway` pattern exists:
   ```bash
   oc get routes -n redhat-ods-applications -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.host}{"\n"}{end}' | grep data-science-gateway
   ```
3. Check the route's target service and TLS configuration:
   ```bash
   oc get route data-science-gateway -n redhat-ods-applications -o yaml
   ```
4. Verify the route's TLS termination and certificate settings are correct

**Expected Results**:
- A route matching the `data-science-gateway` pattern exists in the RHOAI namespace
- The route targets the correct dashboard service with proper port mapping
- TLS termination is configured (edge or reencrypt) with valid certificates
- The route status shows the route as admitted by the ingress controller

**Notes**: To be filled later in the process.

---
feature: dashboard_route_redirect
source_key: RHOAIENG-48787
score: 8
pass: true
verdict: Ready
scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 1
  consistency: 1
last_updated: '2026-04-22'
auto_revised: false
before_score: 6
before_scores:
  specificity: 2
  grounding: 1
  scope_fidelity: 1
  actionability: 1
  consistency: 1
error: null
---
## Rubric Scores

| Criterion | Score | Notes |
|-----------|-------|-------|
| Specificity | 2/2 | P0 definition names route migration from `rhods-dashboard-redhat-ods-applications` to `data-science-gateway`, "Unauthorized" messages, bookmarked URL breakage. Risks name user lockout during upgrade window, SSO configuration breakage, console link discovery. Passes swap test. |
| Grounding | 2/2 | All Section 4 entries traceable to strategy text. Unknowns marked TBD with document type that would resolve them. 3 grounded entries, 0 fabrications. |
| Scope Fidelity | 2/2 | Strategy items (dashboard inaccessible, old vs new route, errors, console discovery) all map to test objectives 1-5 and Section 4. No orphans in either direction. |
| Actionability | 1/2 | TBDs marked with rationale and resolution document types. Route patterns provided as examples. Test users have defined roles. But no exact OCP version, no exact RHOAI builds, no sample commands, no concrete cluster sizing. Too many unknowns for a 2. |
| Consistency | 1/2 | 5 of 6 cross-checks pass. Section 6.2 E2E coverage matrix is entirely empty — does not list P0 endpoints as row stubs. Minor gap since section says "to be filled later." |

**Total: 8/10 — Verdict: Ready**

## Grounding Cross-Reference

| Section 4 Entry | Source Match | Status |
|-----------------|-------------|--------|
| `rhods-dashboard-redhat-ods-applications/*` (OpenShift Route, P0) | "The route changes from the rhods-dashboard-redhat-ods-applications pattern to data-science-gateway" and "Attempt to access the dashboard at the previously bookmarked/known URL" | Grounded |
| `data-science-gateway/*` (OpenShift Route, P0) | "The route changes from the rhods-dashboard-redhat-ods-applications pattern to data-science-gateway" | Grounded |
| OpenShift Console -> "Red Hat OpenShift AI" link (UI Navigation, P1) | "Users must manually navigate to the OpenShift console and locate the new route via the 'Red Hat OpenShift AI' link under 'OpenShift Self Managed Services' to regain access." | Grounded |

## Section-by-Section Feedback

### Actionability (1/2)

A QE engineer picking up this test plan would still need to ask several clarifying questions before they could begin execution:

1. **OCP version**: Specify the exact OpenShift Container Platform version(s) under test (e.g., OCP 4.14, 4.15). The strategy likely constrains the supported matrix.
2. **RHOAI builds**: Identify the exact pre-3.3 RHOAI build to use as the upgrade source and the target 3.3 build. Include build identifiers or catalog source references.
3. **Full route URLs**: Replace example route patterns with complete, copy-pasteable URLs including the cluster domain placeholder (e.g., `https://rhods-dashboard-redhat-ods-applications.apps.<cluster>/`).
4. **Cluster sizing**: Specify minimum node count, instance types, and resource requirements for the test environment.
5. **Sample commands**: Add concrete `oc` commands for verifying route existence, checking route status, and confirming the migration (e.g., `oc get route -n redhat-ods-applications`).
6. **Reduce TBD count in tools/config**: Resolve or narrow the TBDs in the tooling and configuration sections by referencing standard RHOAI QE tooling conventions.

### Consistency (1/2)

1. **Section 6.2 E2E coverage matrix**: Populate the matrix with P0 endpoint names as row stubs. At minimum, list:
   - `rhods-dashboard-redhat-ods-applications/*` (old route)
   - `data-science-gateway/*` (new route)
   - OpenShift Console "Red Hat OpenShift AI" link

   Even if column values are TBD, the row headers should reflect the P0 endpoints identified in Section 4 to maintain traceability.

## Revision History

Initial assessment

### Cycle 1 Revision
- **Specificity**: N/A — scored 2
- **Grounding**: Removed extrapolated "Dashboard Route (OpenShift Route resource)" entry from Section 4. Reframed redirect/notification language throughout Sections 1.2, 1.3, 2.1, 2.2, and 4 as conditional ("via whichever mechanism the implementation delivers") rather than confirmed implementations. Updated Section 10.2 to match revised Section 4.
- **Scope Fidelity**: Removed "Rollback scenarios and route reversion behavior" from Section 1.2 In Scope. Removed Test Objective 6 (rollback). Removed rollback from Section 7.2 NFR and Section 5.2 naming conventions. Replaced rollback risk row in Section 8 with access-continuity mechanism risk. Reframed objectives to verify user access continuity rather than specific fix architectures.
- **Actionability**: Added resolution document types to all TBDs in Sections 3.1, 3.2, 3.3, 9.1, 9.2, and 9.3 (e.g., "pending release manifest," "pending engineering design document," "pending QE automation framework documentation"). Added specific placeholders for OpenShift version, pre-3.3 RHOAI version, RHOAI 3.3 build identifier, namespace configuration, and minimum cluster resources in Section 3.1.
- **Consistency**: Reframed Section 2.1 "Functional Testing" to remove reference to unconfirmed "notification systems" and instead reference the implemented mechanism generically. Removed documentation testing from P2 in Section 2.3 since no test level covers it. Removed rollback from Section 7.2 NFR entries. Updated Section 10.2 endpoint list to match revised Section 4 (3 entries instead of 4).

This is a re-assessment after Cycle 1 revision. The test plan was previously revised to address feedback from the initial review cycle.


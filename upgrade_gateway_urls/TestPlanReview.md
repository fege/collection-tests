---
feature: upgrade_gateway_urls
source_key: RHOAIENG-48747
score: 7
pass: true
verdict: Revise
scores:
  specificity: 2
  grounding: 1
  scope_fidelity: 2
  actionability: 1
  consistency: 1
last_updated: '2026-04-28'
auto_revised: false
before_score: 9
before_scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 1
  consistency: 2
error: score_regression:9->7
---
# Test Plan Review: upgrade_gateway_urls

**Source**: [RHOAIENG-48747](https://redhat.atlassian.net/browse/RHOAIENG-48747)
**Verdict**: Revise
**Total Score**: 7/10

---

## Score Summary

| Criterion | Score | Max | Evidence |
|-----------|-------|-----|----------|
| Specificity | 2 | 2 | Risks cite port 80/targetPort 8888 mismatch, HTTPRoute "Accepted" status masking failures, three workbench types, inject-auth annotation semantics. Feature-specific, passes smell test. |
| Grounding | 1 | 2 | 6/7 entries grounded. "Gateway-based workbench URLs (post-migration)" is extrapolated -- strategy mentions broken Gateway URLs but does not explicitly describe testing working Gateway URLs post-migration as a distinct endpoint. |
| Scope Fidelity | 2 | 2 | All strategy deliverables mapped. No scope creep. Out-of-scope items reasonable. No orphans in either direction. |
| Actionability | 1 | 2 | OCP 4.16+ specified but exact version TBD. Detailed workbench creation steps, but Code-server/RStudio image names TBD. Admin/Regular user roles with oc commands, but credentials TBD. Operator channels TBD. Approximately 4 clarifying questions remain. |
| Consistency | 1 | 2 | 5/6 cross-checks pass. Section 6.2 E2E coverage table is completely empty (not even structural mapping of P0 endpoints). |

---

## Grounding Cross-Reference

| Section 4 Entry | Source Match | Status |
|-----------------|-------------|--------|
| Route URL Code-server/RStudio | Strategy verbatim | Grounded |
| Route URL JupyterLab | Strategy verbatim | Grounded |
| Dashboard workbench URL display | Strategy: "the Dashboard displays new Gateway-based URLs" | Grounded |
| HTTPRoute resource | Strategy: "creates HTTPRoutes pointing to service port 8888" | Grounded |
| Service port config | Strategy: "base notebook service only exposes port 80 (with targetPort 8888)" | Grounded |
| inject-auth annotation | Strategy: "unmigrated notebooks (those without inject-auth: true)" | Grounded |
| Gateway URLs post-migration | Strategy mentions broken Gateway URLs but not working post-migration as a distinct testable endpoint | Extrapolated |

---

## Consistency Cross-Checks

| Check | Result |
|-------|--------|
| Section 4 vs 1.2 (scope alignment) | PASS |
| Section 2.1 vs Section 4 (test levels cover all endpoints) | PASS |
| Section 4 priorities vs 2.3 (priority definitions match endpoint priorities) | PASS |
| Section 10.2 vs Section 4 (appendix coverage table lists all endpoints) | PASS |
| Section 7 NFR vs scope (NFRs address or explicitly exclude relevant categories) | PASS |
| Section 6.2 E2E vs P0 (every P0 endpoint has at least one E2E scenario) | FAIL -- Section 6.2 is completely empty |

---

## Section-by-Section Feedback

The following sections provide targeted feedback for each criterion that scored below 2. Criteria that scored 2 (Specificity, Scope Fidelity) are satisfactory and do not require changes.

### Grounding (Score: 1)

**Section 4, Row 7 -- "Gateway-based workbench URLs (post-migration)"**

This endpoint is extrapolated. The strategy describes the bug (broken Gateway URLs for unmigrated workbenches) and mentions the migration path (adding `inject-auth: true`), but does not explicitly call out "working Gateway URLs post-migration" as a separate testable endpoint distinct from the HTTPRoute resource itself. The test plan introduced this as a P1 endpoint without direct strategy backing.

Recommended fix: Either ground this entry by citing a specific strategy statement that describes verifying post-migration Gateway URL accessibility as a distinct concern, or fold this endpoint into the existing "HTTPRoute resource for unmigrated workbenches" row with a note that post-migration validation is part of the migration test path. Alternatively, re-label the entry to make clear it is a derived test target (e.g., "Migrated workbench accessibility via HTTPRoute -- derived from migration path described in strategy").

### Actionability (Score: 1)

The plan has substantially detailed steps but retains multiple TBD placeholders in provisioning-critical paths:

1. **Section 3.1 -- OCP version**: "OCP 4.16 or later (TBD -- pending release compatibility matrix for RHOAI 3.3)". The exact OCP version affects Gateway API CRD availability and cluster provisioning. Resolve by confirming the minimum OCP version from the RHOAI 3.3 compatibility matrix, or specify a concrete version to use for testing (e.g., "OCP 4.16.14").

2. **Section 3.2 -- Notebook image names**: Code-server and RStudio image names are marked TBD. These are needed to create the pre-upgrade workbenches. Resolve by checking the RHOAI 2.25 image list (`oc get imagestream -n redhat-ods-applications`) or referencing the product documentation for shipped notebook images.

3. **Section 3.3 -- User credentials**: Both Admin and Regular user credentials are TBD. While credentials are environment-specific, the plan should specify whether to use `kubeadmin`, an LDAP-backed user, or an htpasswd identity provider, and provide the setup commands. The current text mentions `kubeadmin` as an option but does not commit to a concrete approach.

4. **Section 9.2 -- Operator channels**: Both the 2.25 and 3.3 operator channel names are TBD. These are essential for the upgrade procedure. Resolve by confirming the channel names from the operator catalog or specifying the `oc` commands to discover them at test time (e.g., `oc get packagemanifest rhods-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'`).

Each TBD should be resolved with either a concrete value or a documented discovery step that the tester can execute before beginning the test.

### Consistency (Score: 1)

**Section 6.2 -- E2E Coverage Matrix is empty.**

Section 6.2 contains only empty table headers with no content. While this section is noted as "To be filled later in the process" (pre-create-cases state), the rubric requires at least structural mapping of P0 endpoints to placeholder E2E scenario IDs. The five P0 endpoints from Section 4 should each have at least a placeholder row in the coverage matrix indicating that E2E coverage is planned.

Recommended fix: Populate Section 6.2 with one row per Section 4 endpoint, using placeholder scenario IDs (e.g., "TBD -- E2E-001") to establish the structural mapping. This demonstrates that coverage planning has been considered even before test case generation. For example:

```
| Endpoint (from Section 4)                          | E2E Scenarios |
|----------------------------------------------------|---------------|
| Route URL Code-server/RStudio                      | TBD           |
| Route URL JupyterLab                               | TBD           |
| Dashboard workbench URL display                    | TBD           |
| HTTPRoute resource for unmigrated workbenches      | TBD           |
| Notebook Service port configuration                | TBD           |
| Workbench inject-auth annotation                   | TBD           |
| Gateway-based workbench URLs (post-migration)      | TBD           |
```

---

## Verdict Rationale

Total score is 7/10 with no criterion scoring 0. Per the rubric:
- Total >= 8 with no 0s would be **Ready**
- Total = 7 with no 0s is **Revise**
- Total < 7 or any 0 would be **Rework**

The plan is structurally sound and well-aligned with the strategy. Three criteria need improvement: one extrapolated endpoint needs grounding or re-labeling, several TBD placeholders need resolution for actionability, and the E2E coverage matrix needs at least structural population. These are addressable revisions that do not require fundamental restructuring.

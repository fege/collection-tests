---
feature: automated_upgrade_validation
source_key: RHAISTRAT-1519
score: 9
pass: true
verdict: Ready
scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 1
  consistency: 2
last_updated: '2026-05-04'
auto_revised: false
before_score: 9
before_scores:
  specificity: 2
  grounding: 2
  scope_fidelity: 2
  actionability: 1
  consistency: 2
error: null
---
## Rubric Scores

| Criterion | Score | Notes |
|-----------|-------|-------|
| Specificity | 2/2 | Priorities, risks, and test levels are feature-specific. Risks reference concrete Jira tickets (RHOAIENG-60207), confirmed blockers (Director container x86_64-only), and feature-specific failure modes. |
| Grounding | 2/2 | All 15 Section 4 entries trace directly to strategy text. No fabricated endpoints or invented technical details. Unknowns marked as TBD with appropriate context. |
| Scope Fidelity | 2/2 | Every strategy requirement maps to a test objective. Refinement additions (disconnected mode, Bodies of Water, workload continuity) properly incorporated. Out-of-scope items correctly excluded. |
| Actionability | 1/2 | RHOAI versions, OpenShift version, and specific tools listed. However, component operators, test users, scenario manifest format, CI platform, and cluster provisioning tooling are TBD without resolution paths. |
| Consistency | 2/2 | All six cross-checks pass. Section 4 entries align with scope, test levels, priorities, NFR assessments, and appendix coverage table. |

**Total: 9/10 -- Verdict: Ready**

## Grounding Cross-Reference

| Section 4 Entry | Source Match | Status |
|---|---|---|
| Upgrade pipeline trigger | "Every produced release artifact...must execute the supported upgrade matrix" | Grounded |
| Cluster deployment (pre-upgrade version) | "Deploy a cluster running the pre-upgrade version" (step 1) | Grounded |
| Component scenario deployment | "Deploy pre-upgrade component-owned and cross-component scenarios" (step 2) | Grounded |
| Cross-component scenario deployment | "QE and component teams provide realistic, multi-component GitOps-based workflows deployed pre-upgrade" | Grounded |
| Pre-upgrade checks | "Run pre-upgrade checks and prerequisite resolution" (step 3) | Grounded |
| Prerequisite resolution | "odh-cli provides documented helpers for pre-upgrade checks, prerequisite resolution" | Grounded |
| Platform upgrade execution | "Execute the platform upgrade" (step 4) | Grounded |
| Post-upgrade steps | "Run post-upgrade steps" (step 5) | Grounded |
| Component-owned scenario validation | "Validate component-owned scenarios" (step 6) | Grounded |
| Cross-component workflow validation | "Validate cross-component customer workflows" (step 7) | Grounded |
| Result publication | "Publish attributable results" (step 8) | Grounded |
| Failure attribution | "Failures are attributable to component or platform owners" | Grounded |
| Upgrade matrix configuration | "New upgrade paths can be added through the upgrade matrix without pipeline redesign" | Grounded |
| Release-readiness gate check | "The upgrade validation signal is used as a release-readiness gate before EA" | Grounded |
| odh-cli helpers | "odh-cli includes documented helpers for prerequisite resolution and post-upgrade steps" | Grounded |

## Section-by-Section Feedback

### Section 3.1 / 3.3 / 9.1 -- Actionability (1/2)

The following TBD items lack resolution paths or timelines:

1. **Component operators** (Section 3.1): "TBD (specific versions depend on component workload specifications)" -- Add what document or process will determine these versions and a target date.

2. **Component service accounts** (Section 3.3): "TBD (specific RBAC requirements depend on component workload types)" -- Add what document or team interaction will resolve this.

3. **Namespace-scoped users** (Section 3.3): "TBD (for validating workload isolation)" -- Add whether this is dependent on a design doc or component team input.

4. **GitOps scenario manifests** (Section 3.2): Described as a requirement but no format, schema, example, or repository location provided. Even a minimal example YAML would improve actionability.

5. **Component ownership mapping** (Section 3.2): Described as a "data structure" but no format or example. An engineer would need to ask what this looks like.

6. **CI pipeline infrastructure** (Section 9.1): "capable of triggering validation on every release artifact" -- No specific CI system named (Tekton, Jenkins, GitHub Actions).

7. **Cluster provisioning automation** (Section 9.1): "deploys clusters at specific RHOAI versions" -- No tooling specified (Hive, ACM, ROSA CLI).

**Note**: These are legitimate gaps documented in TestPlanGaps.md and are expected to be resolved by design docs or ADRs. The test plan correctly marks them as TBD rather than fabricating details.

## Revision History

Initial assessment.

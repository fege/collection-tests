---
test_case_id: TC-RDR-002
source_key: RHOAIENG-48787
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-RDR-002: Old route subpaths resolve after upgrade

**Objective**: Verify that subpaths under the old route (e.g., bookmarked deep links) are handled gracefully after upgrade, not just the root path.

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Old route URL recorded before upgrade

**Test Steps**:
1. Access the old route root path:
   ```bash
   curl -sSL -o /dev/null -w "%{http_code} %{redirect_url}" "https://${OLD_ROUTE}/"
   ```
2. Access a common dashboard subpath under the old route:
   ```bash
   curl -sSL -o /dev/null -w "%{http_code} %{redirect_url}" "https://${OLD_ROUTE}/projects"
   ```
3. Access another subpath:
   ```bash
   curl -sSL -o /dev/null -w "%{http_code} %{redirect_url}" "https://${OLD_ROUTE}/notebookController"
   ```
4. For each subpath, verify the response — redirect, error page, or informational message

**Expected Results**:
- Subpaths under the old route are handled consistently with the root path behavior
- Users accessing deep-linked bookmarks are not shown raw 404 or 401 errors
- If redirects are implemented, subpath information is preserved in the redirect target (e.g., `/projects` redirects to the corresponding new route subpath)

**Notes**: To be filled later in the process.

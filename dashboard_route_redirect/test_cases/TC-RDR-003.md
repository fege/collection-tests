---
test_case_id: TC-RDR-003
source_key: RHOAIENG-48787
priority: P1
status: Draft
automation_status: Not Started
last_updated: '2026-04-22'
---
# TC-RDR-003: HTTP response from old route is appropriate after upgrade

**Objective**: Verify that the HTTP response code and headers from the old route after upgrade are appropriate (not a misleading 401/403).

**Preconditions**:
- RHOAI upgrade from pre-3.3 to 3.3 completed
- Old route URL recorded before upgrade

**Test Steps**:
1. Send a request to the old route and capture the full HTTP response:
   ```bash
   curl -sSL -D - "https://${OLD_ROUTE}" -o /dev/null
   ```
2. Check the HTTP status code — it should NOT be 401 or 403 unless the user is genuinely unauthenticated
3. If a redirect (301/302), verify the Location header
4. If the old route no longer exists (404 or connection refused), verify whether an alternative access-continuity mechanism is in place
5. Send a request with valid authentication credentials and verify the response:
   ```bash
   curl -sSL -D - -H "Authorization: Bearer $(oc whoami -t)" "https://${OLD_ROUTE}" -o /dev/null
   ```

**Expected Results**:
- Authenticated users do NOT receive a 401 "Unauthorized" response at the old route
- The HTTP status code accurately reflects the state: 301/302 for redirect, 200 if route still serves, or a clear informational page — not a misleading auth error
- Response headers do not contain stale or incorrect authentication challenge headers

**Notes**: To be filled later in the process.

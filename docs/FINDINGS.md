# Findings & Observations

This document records API behaviours observed during testing and potential improvements to response handling. The suggested outcomes below are review proposals, not confirmed requirements of this API.

Some automated checks capture the observed behaviour as a regression baseline. Passing these checks does not mean the API is defect-free. Each observation should be reviewed against the API documentation and agreed requirements before being classified as a confirmed defect.

---

### F-01 — `DELETE /booking/:id` returns `201 Created`

| | |
|---|---|
| **Endpoint** | `DELETE /booking/:id` |
| **Severity** | To be assessed — deletion succeeds, but the response status merits review |
| **Suggested behaviour** | `200 OK` or `204 No Content` would better communicate a successful deletion. This is a proposed improvement, not a verified requirement of this API. |
| **Steps** | 1. Authenticate and obtain a token · 2. Create a booking · 3. Send `DELETE /booking/:id` with the token |
| **Expected** | `200 OK` or `204 No Content` |
| **Actual** | `201 Created` |
| **Notes** | `201` signals that a resource was *created*, which is semantically wrong for a deletion. The record is in fact removed (a follow-up `GET` returns `404`), so the impact is limited to API correctness/contract. |

---

### F-02 — `DELETE` on a non-existent booking returns `405`

| | |
|---|---|
| **Endpoint** | `DELETE /booking/:id` (unknown id) |
| **Severity** | To be assessed — unexpected response semantics; client impact not yet demonstrated |
| **Steps** | 1. Authenticate and obtain a valid token. 2. Create a booking and record its ID. 3. Delete that booking using the token. 4. Confirm that GET /booking/:id returns 404. 5. Send DELETE /booking/:id again using the same valid token and record the response. |
| **Suggested behaviour** | `404 Not Found` would clearly indicate that the booking does not exist. This is a proposed improvement, not a verified requirement of this API. |
| **Actual** | `405 Method Not Allowed` |
| **Notes** | `405` implies the HTTP method is not supported on the resource, which is misleading — the method is supported, the resource simply does not exist. |
| **Verification** | Manually reproduced in Postman: created a booking, deleted it (201 Created), confirmed GET returned 404 Not Found, then repeated DELETE for the same ID using the same token. The second DELETE returned 405 Method Not Allowed with body `Method Not Allowed`. |

---

### F-03 — `POST /auth` with invalid credentials returns `200 OK`

| | |
|---|---|
| **Endpoint** | `POST /auth` |
| **Severity** | To be assessed — response-handling concern; no authentication bypass demonstrated |
| **Steps** | 1. `POST /auth` with a wrong password |
| **Suggested behaviour** | Consider an appropriate non-2xx response for rejected credentials, consistent with the API's authentication contract. The required status code has not yet been verified against the specification. |
| **Actual** | `200 OK` with body `{ "reason": "Bad credentials" }` |
| **Notes** | Returning `200` for a failed authentication is poor practice: clients that only check the status code would treat the call as successful. The failure is only detectable by inspecting the response body. The suite asserts both the (quirky) status code and the absence of a token. |

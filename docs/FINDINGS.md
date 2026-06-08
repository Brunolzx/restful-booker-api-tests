# Findings & Observations

Restful-Booker is intentionally seeded with bugs so testers can practise spotting them.
The test suite **asserts the API's _actual_ behaviour** (so the build stays green and stable),
while this document records the **_expected_ behaviour** per REST conventions. Distinguishing
"what the API does" from "what it should do" is the core of the job — a green suite that hides
known defects is worse than a green suite that documents them.

---

### F-01 — `DELETE /booking/:id` returns `201 Created`

| | |
|---|---|
| **Endpoint** | `DELETE /booking/:id` |
| **Severity** | Low (functional behaviour is correct; status code is wrong) |
| **Steps** | 1. Authenticate and obtain a token · 2. Create a booking · 3. Send `DELETE /booking/:id` with the token |
| **Expected** | `200 OK` or `204 No Content` |
| **Actual** | `201 Created` |
| **Notes** | `201` signals that a resource was *created*, which is semantically wrong for a deletion. The record is in fact removed (a follow-up `GET` returns `404`), so the impact is limited to API correctness/contract. |

---

### F-02 — `DELETE` on a non-existent booking returns `405`

| | |
|---|---|
| **Endpoint** | `DELETE /booking/:id` (unknown id) |
| **Severity** | Low |
| **Steps** | 1. Authenticate · 2. `DELETE /booking/9999999` |
| **Expected** | `404 Not Found` |
| **Actual** | `405 Method Not Allowed` |
| **Notes** | `405` implies the HTTP method is not supported on the resource, which is misleading — the method is supported, the resource simply does not exist. |

---

### F-03 — `POST /auth` with invalid credentials returns `200 OK`

| | |
|---|---|
| **Endpoint** | `POST /auth` |
| **Severity** | Medium (security/contract) |
| **Steps** | 1. `POST /auth` with a wrong password |
| **Expected** | `401 Unauthorized` |
| **Actual** | `200 OK` with body `{ "reason": "Bad credentials" }` |
| **Notes** | Returning `200` for a failed authentication is poor practice: clients that only check the status code would treat the call as successful. The failure is only detectable by inspecting the response body. The suite asserts both the (quirky) status code and the absence of a token. |

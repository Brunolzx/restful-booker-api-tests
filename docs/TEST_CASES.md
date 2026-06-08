# Test Cases

Manual test-case documentation for the Restful-Booker API. Each case is automated in the
Postman collection (`postman/Restful-Booker.postman_collection.json`); this document is the
human-readable design behind those tests — useful for reviews, traceability, and onboarding.

**Base URL:** `https://restful-booker.herokuapp.com`
**Auth:** `POST /auth` with `admin` / `password123` returns a token used for `PUT`, `PATCH`, `DELETE`.

| ID | Title | Type | Preconditions | Steps | Expected Result |
|----|-------|------|---------------|-------|-----------------|
| TC-01 | Health check | Positive | — | `GET /ping` | `201`; response under 2000 ms |
| TC-02 | Authenticate with valid credentials | Positive | Valid admin credentials | `POST /auth` with correct user/password | `200`; body contains a non-empty `token` |
| TC-03 | Reject invalid credentials | Negative | — | `POST /auth` with a wrong password | No `token`; body `{ reason: "Bad credentials" }` (see F-03) |
| TC-04 | Create a booking | Positive | — | `POST /booking` with a valid payload | `200`; numeric `bookingid`; response echoes sent data; schema valid |
| TC-05 | Read a booking by id | Positive | A booking exists (TC-04) | `GET /booking/:id` | `200`; fields match the created booking |
| TC-06 | List all booking ids | Positive | At least one booking exists | `GET /booking` | `200`; non-empty array of objects with `bookingid` |
| TC-07 | Filter bookings by name | Positive | A matching booking exists | `GET /booking?firstname=&lastname=` | `200`; response is an array |
| TC-08 | Reject update without a token | Negative | A booking exists | `PUT /booking/:id` with no token | `403 Forbidden` |
| TC-09 | Full update with a token | Positive | Valid token (TC-02), booking exists | `PUT /booking/:id` with token + full payload | `200`; all fields reflect the new values |
| TC-10 | Partial update with a token | Positive | Valid token, booking exists | `PATCH /booking/:id` changing one field | `200`; only the targeted field changes, others unchanged |
| TC-11 | Delete a booking | Positive | Valid token, booking exists | `DELETE /booking/:id` with token | `201` (documented quirk, see F-01) |
| TC-12 | Deleted booking is gone | Positive | TC-11 executed | `GET /booking/:id` | `404 Not Found` |
| TC-13 | Read a non-existent booking | Negative | — | `GET /booking/9999999` | `404 Not Found` |

> The suite runs the cases in order: authentication and creation feed later requests through
> chained variables (`token`, `bookingid`), mirroring a real CRUD workflow.

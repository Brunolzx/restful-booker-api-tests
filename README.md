<div align="center">

# Restful-Booker — API Test Suite

[![API Tests](https://github.com/Brunolzx/restful-booker-api-tests/actions/workflows/api-tests.yml/badge.svg)](https://github.com/Brunolzx/restful-booker-api-tests/actions/workflows/api-tests.yml)
![Postman](https://img.shields.io/badge/Postman-FF6C37?style=flat&logo=postman&logoColor=white)
![Newman](https://img.shields.io/badge/Newman-FF6C37?style=flat&logo=postman&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-20+-339933?style=flat&logo=nodedotjs&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/CI-GitHub_Actions-2088FF?style=flat&logo=githubactions&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-2E8B7F?style=flat)

End-to-end API test suite for the [Restful-Booker API](https://restful-booker.herokuapp.com), built with **Postman** and run in CI with **Newman**.

**13 test cases** · Auth + full CRUD · Request chaining · Schema & field validation · Negative testing · Documented findings

</div>

---

## What this project demonstrates

- **Authentication flow** — obtain a token and reuse it for protected requests
- **Full CRUD** — `POST` → `GET` → `PUT` → `PATCH` → `DELETE`, against an API that actually persists data
- **Request chaining** — capture `bookingid` and `token` from responses and feed them into later requests via collection variables
- **Layered assertions** — status codes, response time, headers, **JSON schema**, and **specific field values** (not just "is the API alive?")
- **Negative testing** — invalid credentials, missing token, and non-existent resources
- **Defect awareness** — the API is intentionally buggy; real bugs are recorded in [`docs/FINDINGS.md`](docs/FINDINGS.md) while the suite asserts actual behaviour to stay green
- **CI/CD** — GitHub Actions runs the full suite on every push & PR and publishes an HTML report

---

## Test coverage

| Folder | Scenarios |
|--------|-----------|
| 00 — Health Check | API availability (`/ping`) |
| 01 — Authentication | Valid token issuance · invalid credentials rejected |
| 02 — Create Booking | Create + schema & field validation |
| 03 — Read Bookings | Read by id · list all · filter by name |
| 04 — Update Booking | Update without token (403) · full `PUT` · partial `PATCH` |
| 05 — Delete Booking | Delete · confirm deletion (404) |
| 06 — Negative & Edge | Non-existent booking (404) |

Full case-by-case design: [`docs/TEST_CASES.md`](docs/TEST_CASES.md).

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **Postman** | Collection authoring & test scripts |
| **Newman** | CLI test runner (used in CI) |
| **newman-reporter-htmlextra** | Rich HTML test report |
| **GitHub Actions** | Continuous integration |

---

## Project Structure

```
├── .github/
│   └── workflows/
│       └── api-tests.yml                       # CI — runs Newman on push/PR
├── postman/
│   ├── Restful-Booker.postman_collection.json  # the test suite
│   └── Restful-Booker.postman_environment.json # baseUrl + credentials
├── docs/
│   ├── TEST_CASES.md                           # manual test-case design
│   └── FINDINGS.md                             # documented API defects
├── package.json
└── README.md
```

---

## Run it

### Option A — Postman (GUI)
1. Import both files from `postman/` (collection + environment).
2. Select the **Restful-Booker - Public** environment.
3. Run the collection with the Collection Runner.

### Option B — Newman (CLI)
```bash
npm install

# run in the terminal
npm test

# run and generate an HTML report into reports/report.html
npm run test:report
```

> The suite is **self-contained**: it creates the data it needs and cleans up after itself, so it can run repeatedly without manual setup. The public API sleeps when idle and resets every 10 minutes, so the first run may be slow.

---

## CI/CD

Every **push** and **pull request** to `main` triggers the [API Tests workflow](.github/workflows/api-tests.yml), which installs dependencies, runs the suite with Newman, and uploads the HTML report as a build artifact.

---

## Author

**Bruno Moreira** — QA Engineer · ISTQB® CTFL

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/bruno-moreira-87439a404)
[![Email](https://img.shields.io/badge/Email-2E8B7F?style=flat&logo=gmail&logoColor=white)](mailto:brunofbarbosam@gmail.com)

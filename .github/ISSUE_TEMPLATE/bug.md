---
name: Bug
about: Something does not work as the contract, the SRS or the documentation says
title: "bug: <short description>"
labels: bug
assignees: ""
---

**What happened**
A clear description of the problem.

**Expected behaviour**
What should happen, with a reference (OpenAPI operation, FR-Mx-xx, US-xx, NFR-xxx-xx).

**Steps to reproduce**
1. Start the stack with `docker compose up`
2. …
3. …

**Request / response** (if it is an API bug)
```http
POST /api/v1/...
```
```json
{ "status": 500, "code": "..." }
```

**Environment**
- Branch / commit: `develop` @ `<sha>`
- OS and browser: …

**Severity:** Blocker / Major / Minor

**Logs or screenshots**
Remove personal data first; only synthetic test data (KVKK).

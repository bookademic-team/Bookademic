# Test Plan and Quality Assurance Strategy

| Field | Value |
|---|---|
| Owner | Mirac – Quality Assurance & Test Automation Engineer |
| Version | Sprint 2 (first version: Sprint 1, `v0.1.0-sprint1`) |
| Related | [NFR specification](sprint2/nfr.md) (#66), [OpenAPI v0.2.3](api/openapi.yaml), [ADR-0001](adr/0001-tech-stack.md), [ADR-0002](adr/0002-authentication-with-jwt.md), [ADR-0003](adr/0003-notification-events-and-appointment-completion.md), [Database schema](architecture/database.md) |

## 1. Overview and Quality Objectives
This test plan defines the verification and validation strategy for the Bookademic modular monolith core. The target release scope focuses on scheduling consistency, appointment lifecycle integrity, and catalog search precision across the four components `M1 Identity`, `M2 Booking Core`, `M3 Lab` and `M4 Office Hours`. Measurable quality targets are defined in [nfr.md](sprint2/nfr.md); section 6 maps each of them to a test.

Quality objectives:
- 0 critical or blocking defects in core interval reservation logic.
- 100% specification conformance against the OpenAPI v0.2.3 contract.
- Automated regression suite coverage on domain logic and state transitions: CI enforces ≥ 50% line coverage (course requirement, ADR-0001); team target 80% on domain logic.
- Concurrency safety validated at the database engine level (PostgreSQL EXCLUDE constraints).

---

## 2. Test Scope Matrix

| Target ID | Scope / Focus | Level | Automated / Manual | Tooling |
|---|---|---|---|---|
| US-01 / US-02 | Institutional registration (US-01), JWT authentication & role-based route guards (US-02) | Integration | Automated | xUnit, Testcontainers, WebApplicationFactory |
| US-06 | Lab device reservation: 400 `INVALID_INTERVAL` for end before start or start in the past (v0.2.3), 409 overlap with pending/approved/checked-in, 403 LAB_APPROVAL_REQUIRED / TRAINING_REQUIRED | Integration | Automated | xUnit, PostgreSQL Testcontainers |
| US-07 | Interval exclusion lock & state-machine lifecycle (Double-booking engine & access rules) | Architecture / Integration | Automated | PostgreSQL Testcontainers |
| US-09 | Cancellation before start only (otherwise 409); someone else's booking → 403; status `CANCELLED` frees the interval immediately | Integration | Automated | xUnit, PostgreSQL Testcontainers |
| US-11 / US-12 | Catalog querying, equipment availability, interval filtering | Unit / Integration | Automated | xUnit, PostgreSQL Testcontainers |
| US-14 | Device training authorization & in-app notification to the approving instructor (US-04) | Integration | Automated | xUnit, WebApplicationFactory |
| US-15 | Handover: 6-digit single-use code for an `APPROVED` reservation; verify → `CHECKED_IN`; second use, wrong or expired code → 400 `INVALID_HANDOVER_CODE`; return → `COMPLETED`; wrong state → 409 | Unit / Integration | Automated | xUnit, PostgreSQL Testcontainers |
| US-17 | Staff appointment slot declaration & conflict prevention | Integration / Concurrency | Automated | xUnit, Parallel Task runners |
| US-18 | Appointment booking with topic note (10-500 chars), daily limit 409, slot start in the past → 400 `INVALID_INTERVAL` (v0.2.3) | Integration | Automated | xUnit, PostgreSQL Testcontainers |
| US-20 | Office-hour day cancellation: `SECRETARY`/`ADMIN`/host only (others 403); reason 5–500 chars; all appointments of the day `CANCELLED` in one transaction; one `OFFICE_HOUR_CANCELLED` notification per affected student and no `RESERVATION_CANCELLED` (ADR-0003) | Integration | Automated | xUnit, PostgreSQL Testcontainers |
| US-23 | Status-change e-mail via SMTP | Integration | Automated | xUnit, Mailpit container |
| Web UI | Critical user flows (Login -> Reserve -> Confirmation) | E2E | Automated | Playwright |

---

## 3. Boundary Value Analysis (BVA) & Edge Cases

### 3.1 Interval and Temporal Boundaries (US-06, US-07, US-17)
- Exact adjacent slots: Slot A `[10:00, 10:30)` and Slot B `[10:30, 11:00)` must not trigger 409 conflict.
- Partial overlap: Slot A `[10:00, 10:30)` and Slot B `[10:15, 10:45)` must trigger 409 Conflict with Problem Details RFC 9457 schema.
- Enclosing interval: Slot A `[09:00, 12:00)` attempting reservation on Slot B `[10:00, 11:00)` must fail immediately.
- Identical interval: Slot A `[10:00, 11:00)` and Slot B `[10:00, 11:00)` must trigger 409.
- Released interval: after Slot A is `CANCELLED`, `REJECTED` or `COMPLETED`, the same interval can be booked again (201).
- Empty or reversed interval: `endsAt = startsAt` or `endsAt < startsAt` must reject with 400 `INVALID_INTERVAL`.
- Past start (OpenAPI v0.2.3): `startsAt = now − 1 min` must reject with 400 `INVALID_INTERVAL`; `startsAt = now + 1 min` must be accepted. The same rule applies to booking a slot whose start has passed (US-18).

### 3.1a Date boundaries in Europe/Istanbul (US-13, US-20)
Timestamps are stored in UTC and dates are interpreted in Europe/Istanbul (UTC+3). Each date rule is tested at 23:59, 00:00 and 02:59 Istanbul time, where the UTC date is still the previous day:
- Lab approval `expiresOn` = today is active; `expiresOn` = yesterday is not active and is rejected on creation (400).
- Day cancellation for today cancels only the appointments whose start is on that Istanbul date.

### 3.1b Handover code boundaries (US-15)
- Code format: `000000` and `999999` are valid formats; 5 or 7 digits and non-digits reject with 400 (schema pattern `^[0-9]{6}$`).
- Single use: first correct use → 200 `CHECKED_IN`; second use of the same code → 400.
- Expiry: code used one second after `expiresAt` → 400 `INVALID_HANDOVER_CODE`.
- Wrong attempts: 5th wrong attempt invalidates the code (NFR-SEC-06, pending contract update).

### 3.2 Appointment Subject Field Boundaries (US-18)
- Minimum valid length: 10 characters (Must accept with 201 Created).
- Below minimum boundary: 9 characters (Must reject with 400 Validation Error).
- Maximum valid length: 500 characters (Must accept with 201 Created).
- Above boundary limit: 501 characters (Must reject with 400 Validation Error).
- Null or whitespace-only strings: Must reject with 400 Validation Error.

### 3.3 Pagination and Filtering Boundaries (US-11 / US-12)
- Zero matches: Valid filter criteria returning zero items must return 200 OK with empty array `[]` (not 404 or null).
- Database volume baseline: Catalog search queries evaluated on a populated baseline of 500 items to maintain p95 latency < 200 ms.

---

### 3.4 Office-hour day cancellation (US-20)
- Reason length: 4 characters → 400; 5 and 500 characters → 200; 501 characters → 400.
- Day with 0 appointments → 200 with `cancelledAppointments = 0`, `notifiedStudents = 0`.
- Day with 3 appointments of 3 students → 3 appointments `CANCELLED`, 3 `OFFICE_HOUR_CANCELLED` notifications.
- Already `CANCELLED` or `COMPLETED` appointments on that day are not changed and not counted.
- Callers: `SECRETARY`, `ADMIN`, the schedule host → 200; another `INSTRUCTOR`, a `STUDENT` → 403; unknown `scheduleId` → 404.

---

## 4. Test Environments and Tooling Stack

- **Unit & Integration Framework:** xUnit (.NET 10)
- **Code Coverage & Thresholds:** Coverlet (CI enforces ≥ 50% line coverage (course requirement, ADR-0001); team target 80% on domain logic)
- **Database Isolation:** Testcontainers running PostgreSQL 17 to validate native range exclusion constraints (`btree_gist` / `tstzrange`)
- **Notification Stubbing:** Mailpit local SMTP test server to verify asynchronous email dispatch without external network calls
- **Static Analysis & Linting:** SonarQube quality gate enforcement on CI
- **End-to-End Testing:** Playwright (Chromium headless; Firefox and WebKit for NFR-PORT-05) against local Docker Compose environment
- **Performance Testing:** k6 (`grafana/k6` container) against the compose stack with 500 seeded records (NFR-PERF-01..04)
- **Contract Checks:** Spectral lint on `openapi.yaml`; response validation against the contract schema in integration tests (NFR-MAIN-06)
- **Architecture Tests:** ArchUnitNET in `Bookademic.ArchitectureTests` (NFR-MAIN-03)
- **Accessibility & Front-end Performance:** Lighthouse CI on SCR-01, SCR-04, SCR-07 (NFR-USE-04, NFR-PERF-06)

**Test projects (resolves the ADR-0001 open points, to be confirmed by Mürsel in review):**
- `Bookademic.Tests` – unit tests of all modules; M2 Booking Core tests in the `Booking` folder so that NFR-MAIN-01 can be measured with a coverage filter on `Bookademic.Booking.Core`.
- `Bookademic.IntegrationTests` – Testcontainers (PostgreSQL 17, Mailpit).
- `Bookademic.ArchitectureTests` – ArchUnitNET rules.
- The 50% coverage gate applies to the backend. Front-end unit tests (Vitest + React Testing Library) are added only for `src/api/client.js` and form validation; the Web App is covered mainly by Playwright.

---

## 5. Peer Review & QA Exit Criteria

A Pull Request is certified for merge into `develop` only when:
1. Associated acceptance criteria match the scenario specifications defined above.
2. At least one designated peer review approval is recorded in GitHub.
3. Automated unit and containerized integration test suites pass with zero failures.
4. OpenAPI schemas reflect all updated request/response contracts and RFC 9457 error structures.

---

## 6. NFR Verification Matrix

Every NFR in [nfr.md](sprint2/nfr.md) is verified by at least one test or check below. "Sprint" is the sprint in which the check first runs.

| NFR | Test / check | Level | Tool | Sprint |
|---|---|---|---|---|
| NFR-PERF-01 | `perf/catalog.js`: `GET /resources` with combined filters, p95 < 200 ms on 500 records | Performance | k6 | Final |
| NFR-PERF-02 | `perf/reservation.js`: `POST /reservations`, 50% free / 50% taken intervals, p95 < 200 ms | Performance | k6 | Final |
| NFR-PERF-03 | `perf/read-views.js`: calendar, unread notifications, incoming appointments, p95 < 200 ms | Performance | k6 | Final |
| NFR-PERF-04 | `perf/reservation.js` approve scenario with `mailpit` stopped | Performance | k6 | Final |
| NFR-PERF-05 | Day cancellation with 12 appointments, timing assertion < 500 ms | Integration | xUnit, Testcontainers | 3 |
| NFR-PERF-06 | Lighthouse desktop performance ≥ 80 on SCR-01, SCR-04, SCR-07 | E2E | Lighthouse CI | Final |
| NFR-SEC-01 | Enumerate all 35 operations from `openapi.yaml`; 33 protected ones return 401 without a token | Integration | xUnit, WebApplicationFactory | 3 |
| NFR-SEC-02 | No / malformed / expired / wrong-signature / wrong-audience token → 401 | Integration | xUnit | 3 |
| NFR-SEC-03 | Role × operation matrix and "someone else's record" cases → 403 | Integration | xUnit (data-driven) | 3 |
| NFR-SEC-04 | Marker password not found in `identity.users`, logs or responses | Integration | xUnit, Testcontainers | 3 |
| NFR-SEC-05 | Unknown e-mail and wrong password give identical 401 bodies | Integration | xUnit | 3 |
| NFR-SEC-06 | Handover code cases in §3.1b | Unit / Integration | xUnit | 3 |
| NFR-SEC-07 | SonarQube quality gate: 0 Blocker/Critical vulnerabilities, 0 unreviewed hotspots | Static | SonarQube | 3 |
| NFR-SEC-08 | 6th login in 1 minute → 429 (after contract update) | Integration | xUnit | Final |
| NFR-REL-01 | 20 parallel `POST /reservations` for one interval → 1 × 201, 19 × 409; SQL check for overlapping active rows | Integration / Concurrency | xUnit, Testcontainers | 3 |
| NFR-REL-02 | Boundary cases §3.1 (conflict engine unit tests + API tests) | Unit / Integration | xUnit | 3 |
| NFR-REL-03 | Shared `AssertProblemDetails` helper on every 4xx/5xx response | Integration | xUnit | 3 |
| NFR-REL-04 | One `status_changes` row per transition; forced rollback publishes no event | Integration | xUnit, Testcontainers | 3 |
| NFR-REL-05 | Failure injected during day cancellation → no appointment or slot changed | Integration | xUnit, Testcontainers | 3 |
| NFR-REL-06 | Status change with Mailpit stopped → 200/201, notification row, 3 retries logged | Integration | xUnit, Testcontainers | 3 |
| NFR-REL-07 | Scripted `docker compose down` / `up`; row counts equal; `api` healthy ≤ 60 s | System | Shell script | Final |
| NFR-REL-08 | Playwright main journey SCR-01 → SCR-08, 3 consecutive green runs | E2E | Playwright | 3 |
| NFR-USE-01 | Usability test, 5 students, success ≥ 4/5, median ≤ 2 min | Manual | Figma prototype, MVP | 2, 3 |
| NFR-USE-02 | Click count SCR-08 → approved request ≤ 3 | Manual | Figma prototype, MVP | 2, 3 |
| NFR-USE-03 | Error-state checklist (ui-screens.md §8); Playwright 403 / 409 / empty-list tests | Manual / E2E | Figma, Playwright | 2, 3 |
| NFR-USE-04 | Lighthouse accessibility ≥ 90; keyboard-only booking form | E2E / Manual | Lighthouse CI | Final |
| NFR-USE-05 | SUS questionnaire ≥ 68 | Manual | Questionnaire | Final |
| NFR-MAIN-01 | Coverage of `Bookademic.Booking.Core` ≥ 50% (US-07), CI fails below | Unit | coverlet | 3 |
| NFR-MAIN-02 | Backend coverage ≥ 50%, CI fails below (EN-04) | Unit / Integration | coverlet, ReportGenerator | 3 |
| NFR-MAIN-03 | ArchUnitNET module-boundary rules | Architecture | ArchUnitNET | 3 |
| NFR-MAIN-04 | SonarQube rating A, duplication < 3%, debt ratio < 5% on new code | Static | SonarQube | Final |
| NFR-MAIN-05 | `dotnet format --verify-no-changes`, ESLint | Static | CI | 3 |
| NFR-MAIN-06 | Spectral lint; contract response validation in integration tests | Contract | Spectral, xUnit | 3 |
| NFR-PORT-01 | Clean-machine release checklist: `cp .env.example .env`, `docker compose up` | System | Manual checklist | 3 |
| NFR-PORT-02 | Timed start-up (first build ≤ 5 min, cached ≤ 60 s) | System | Manual checklist | Final |
| NFR-PORT-03 | Start-up confirmed on Windows, macOS and Linux team machines | System | Manual checklist | Final |
| NFR-PORT-04 | `.env.example` covers every variable used in `docker-compose.yml` | Static | CI script | 3 |
| NFR-PORT-05 | Main journey in Chromium, Firefox, WebKit at 1280 × 720 | E2E | Playwright | Final |
| NFR-PORT-06 | Licence reports for .NET and npm dependencies | Static | dotnet-project-licenses, license-checker | Final |
| NFR-PRIV-01 | Schema review; `User` responses contain only `id`, `email`, `fullName`, `role` | Review / Integration | database.md, xUnit | 2, 3 |
| NFR-PRIV-02 | Two-student isolation tests on calendar, notifications, appointments | Integration | xUnit | 3 |
| NFR-PRIV-03 | Seed data generated by script; PR review checklist | Review | PR checklist | 3 |
| NFR-PRIV-04 | Decode issued JWT (allowed claims only); log assertion for secrets | Unit / Integration | xUnit | 3 |

# Bookademic – Software Requirements Specification (SRS)

| Field | Value |
|---|---|
| Document | `docs/sprint2/srs.md` |
| Version | 1.0 (Sprint 2) |
| Owner | Melih (Product Owner & Requirements Engineer) |
| Reviewer | Mirac (QA): testability of every requirement |
| Issue | [#60](https://github.com/bookademic-team/Bookademic/issues/60) |
| Baseline | OpenAPI contract v0.2.2 (v0.2.3 in PR #78), [`vision.md`](../sprint1/vision.md), [Sprint 2 backlog](backlog.md), [ADR-0001](../adr/0001-tech-stack.md) |

---

## 1. Introduction

### 1.1 Purpose

This document specifies the functional requirements of Bookademic and connects them to the user stories, use cases, API operations and screens that realise them. It is written for:

- the development team, to implement Sprint 3 (MVP) and the final release,
- the QA engineer, to derive test cases from each requirement,
- the course instructor and the jury, to check that the requirements are complete and traceable.

Non-functional requirements are specified in [`nfr.md`](nfr.md) (Section 7). The architecture is described in the C4, UML and database documents (Sections 5 and 6).

### 1.2 Scope

Bookademic is a web application for a single university department. It manages, in one flow, the two scarce resources a project student needs: the instructor's time and the laboratory. Every booking is modelled as **a resource + a time slot** and handled by one shared booking core (M2 Booking Core).

The system:

- registers students with a school e-mail address and authorises 5 roles,
- lets instructors and research assistants publish office, support and training hours, and lets students book them with a topic note,
- lets instructors grant category-based lab access approval from an office-hour meeting, and research assistants authorise students after device training,
- lets students reserve lab devices and rooms only when approval and training conditions are met, with no double booking,
- tracks approval, check-out/return and maintenance of devices,
- notifies users in the app (and by e-mail as a Should feature) when the status of their booking changes.

Out of scope for this release (from [`vision.md` §3.2](../sprint1/vision.md)): university SSO/LDAP, recurring reservations, waiting lists, live queues, video meetings, messaging, mobile app, push/SMS, calendar sync, multiple universities, IoT door locks, paid rental.

### 1.3 Definitions, acronyms and abbreviations

Names below follow the shared glossary of the Sprint 2 plan, taken from OpenAPI v0.2.2 and ADR-0001. All documents use them unchanged.

| Term | Meaning |
|---|---|
| Resource | A bookable device or room (`Resource`, `ResourceType`: `DEVICE`, `ROOM`). For appointments, M2 uses the Slot id as the bookable key (resourceId). |
| Device category | Free-text category of a device (e.g. *Oscilloscope*). Lab approvals and training authorisations are per category. |
| Reservation | An M2 booking of a resource for a half-open interval `[startsAt, endsAt)` (`Reservation`). `ReservationKind`: `LAB`, `APPOINTMENT`. |
| Appointment | A student's booking of an office, support or training slot (`Appointment`). Its id is the id of its underlying M2 reservation. |
| Office-hour schedule | A weekly time range defined by a host (`OfficeHourSchedule`). `ScheduleType`: `OFFICE_HOUR`, `SUPPORT`, `TRAINING`. |
| Slot | A fixed-length part of a schedule that one student can book (`Slot`). |
| Lab approval | An instructor's permission for a student to use a device category until an end date (`LabApproval`). |
| Training authorisation | A research assistant's confirmation that a student completed training for a device category (`TrainingAuthorization`). |
| Status change | One entry of a reservation's status history (`StatusChange`). |
| Notification | An in-app message to one user about a status change (`Notification`). |
| Audit log | A record of one role change: target user, old role, new role, the admin who made it and the time (`AuditLog`, table `identity.audit_log`). Not exposed by the API in this release. |
| Handover code | One-time 6-digit code used when a device is handed over (`HandoverCode`). |
| `ReservationStatus` | `PENDING`, `APPROVED`, `REJECTED`, `CANCELLED`, `CHECKED_IN`, `COMPLETED` |
| `Role` | `STUDENT`, `INSTRUCTOR`, `RESEARCH_ASSISTANT`, `SECRETARY`, `ADMIN` |
| M1–M4 | Modules of the modular monolith: M1 Identity, M2 Booking Core, M3 Lab, M4 Office Hours |
| `IBookingEngine`, `IEligibilityPolicy` | M2 contract interfaces (ADR-0001). M3 and M4 create and change reservations only through `IBookingEngine`; M2 asks the `IEligibilityPolicy` of the resource type before creating a reservation. |
| FR / NFR | Functional / non-functional requirement |
| US / UC / SCR | User story (backlog), use case (vision), screen (`ui-screens.md`) |
| JWT, RBAC | JSON Web Token; role-based access control |
| RFC 9457 | Problem Details for HTTP APIs; error responses carry a machine-readable `code` |
| KVKK | Turkish Personal Data Protection Law (Law No. 6698) |
| MVP | Minimum viable product: all Must stories, delivered in Sprint 3 |

### 1.4 References

| Ref | Document |
|---|---|
| R1 | [Project proposal](../sprint1/proposal.md) |
| R2 | [System vision and boundaries](../sprint1/vision.md) |
| R3 | [Product backlog – Sprint 2](backlog.md) (Sprint 1 version: [`sprint1/backlog.md`](../sprint1/backlog.md)) |
| R4 | [OpenAPI contract v0.2.2](../api/openapi.yaml) and [story mapping](../api/story-mapping.md) |
| R5 | [ADR-0001 Technology stack](../adr/0001-tech-stack.md), [ADR-0002 JWT authentication](../adr/0002-authentication-with-jwt.md), [ADR-0003 Notification events and appointment completion](../adr/0003-notification-events-and-appointment-completion.md) |
| R6 | [Screen list and flow](../ui-screens.md); [Figma prototypes](ui-prototypes.md) |
| R7 | [Non-functional requirements](nfr.md) |
| R8 | [C4 model](../architecture/c4.md), [UML](../architecture/uml.md), [database schema](../architecture/database.md), [deployment](../architecture/deployment.md) |
| R9 | [Test plan](../test-plan.md) |
| R10 | [Student survey](../sprint1/survey.md) |
| R11 | IEEE 29148-2018, *Systems and software engineering – Life cycle processes – Requirements engineering* (structure of this SRS) |

### 1.5 Conventions

- **"shall"** marks a mandatory requirement. Each FR is testable: it names the input, the expected result and the error `code`.
- **FR IDs:** `FR-<module>-<no>`, e.g. `FR-M2-03`. The module is the one that owns the related story (backlog label). IDs are never reused.
- **Priority** of an FR is the MoSCoW priority of its user story. **Sprint** says when it is implemented: 3 (MVP) or Final.
- **Error responses** are `application/problem+json` (RFC 9457). The error is identified by `code`, not by the HTTP status alone.
- **API paths** are relative to `http://localhost:8080/api/v1`. Every authenticated call carries `Authorization: Bearer <JWT>`; a missing or invalid token returns `401 UNAUTHORIZED` (FR-M1-04). This is not repeated in each FR.
- **Time:** ISO 8601 with offset; seed data uses Europe/Istanbul (+03:00). "Now" means the server time when the request is processed.

### 1.6 Changes since Sprint 1 and open points

| # | Change / open point | Affected | Status |
|---|---|---|---|
| C1 | **Start time in the past → 400.** Deferred from Sprint 1 (decision D7, [4 Nov record](../meetings/2026-11-04-sprint1-alignment.md)). A lab reservation whose `startsAt` is before now, or an appointment on a slot that has already started, is rejected with `400 INVALID_INTERVAL`. | FR-M2-01, FR-M4-04; US-06, US-18 | Added to the backlog. **OpenAPI v0.2.3** adds the rule to the descriptions of `createReservation` and `bookAppointment`; the existing `INVALID_INTERVAL` code is reused, no new enum value. Owner: Mürsel (confirmed 20 Nov). |
| C2 | **Audit log for role changes** (US-03) had no entity in OpenAPI v0.2.2 or in the glossary. | FR-M1-06 | **Resolved** (Mürsel, 20 Nov): entity `AuditLog`, table `identity.audit_log` ([`database.md`](../architecture/database.md)); added to the glossary (§1.3) and §5. |
| C3 | **Lab approval end date.** `expiresOn` earlier than today is rejected with `400 VALIDATION_ERROR` (an approval that is already expired has no use). | FR-M3-05; US-13 | Added to the backlog; covered by the generic `400` of `grantLabApproval`, no contract change. |
| C4 | **US-04 notification for maintenance.** The `Notification.type` enum has no maintenance-specific value; FR-M3-11 uses `RESERVATION_CANCELLED`. | FR-M3-11 | Confirmed as sufficient; no contract change. |
| C5 | **Testability clarifications** from the QA review of #82: dates in Europe/Istanbul (FR-M3-05, FR-M4-04, FR-M4-07); handover code lifetime `Handover__CodeLifetimeMinutes`, default 15 min (FR-M3-09); `validUntil` from `Semester__EndDate` (FR-M3-07); only `APPROVED` appointments count for the daily limit and for day cancellation; day cancellation for a past date → `400 VALIDATION_ERROR`. | FR-M3-05, FR-M3-07, FR-M3-09, FR-M4-04, FR-M4-07; US-13, US-14, US-15, US-18, US-20 | Added (follow-up to #82). The past-date rule for `cancelOfficeHourDay` uses the existing generic `400`; description to be added in the next OpenAPI version (Mürsel). |

---

## 2. Overall description

### 2.1 Product perspective

Bookademic is a new, self-contained system. It replaces paper lists for lab devices, e-mail or signature approvals, and notes taped to office doors (R1 §1.1).

It is a **modular monolith** (ADR-0001): one web application (React + Vite + Ant Design), one API (ASP.NET Core 10) and one PostgreSQL 17 database, started with `docker compose up`. The only external system is an SMTP service (Mailpit in development).

| Container | Technology | docker-compose service |
|---|---|---|
| Web App | React + Vite + Ant Design | `web` |
| API | ASP.NET Core 10, modular monolith | `api` |
| Database | PostgreSQL 17 | `db` |
| SMTP service (external) | MailKit → Mailpit in development | `mailpit` |

| Component (inside the API) | Responsibility | FR group |
|---|---|---|
| M1 Identity | Registration, login, roles, in-app and e-mail notifications, usage report | FR-M1-xx |
| M2 Booking Core | Shared interval conflict check, reservation state machine, status history, cancellation, calendar | FR-M2-xx |
| M3 Lab | Resource catalogue, lab approval, training authorisation, handover, maintenance | FR-M3-xx |
| M4 Office Hours | Office, support and training hours; slots; appointments; day cancellation; redirection | FR-M4-xx |

M3 and M4 never implement their own conflict logic. Every lab reservation and every appointment is an M2 reservation created through `IBookingEngine`; M3 contributes the lab approval + training rule and M4 the daily appointment limit as `IEligibilityPolicy` implementations. The context and container views are drawn in [`c4.md`](../architecture/c4.md); the context diagram of Sprint 1 is in [`vision.md` §3.3](../sprint1/vision.md).

### 2.2 Product functions

| Function | Main FRs |
|---|---|
| F1 Account and access | FR-M1-01 … FR-M1-06 |
| F2 Notifications and reports | FR-M1-07 … FR-M1-10 |
| F3 Reservation core (conflict check, state machine, cancellation, calendar) | FR-M2-01 … FR-M2-11 |
| F4 Resource catalogue | FR-M3-01 … FR-M3-03 |
| F5 Lab access approval and device training | FR-M3-04 … FR-M3-08 |
| F6 Device handover and maintenance | FR-M3-09 … FR-M3-11 |
| F7 Office, support and training hours; appointments | FR-M4-01 … FR-M4-06 |
| F8 Day cancellation, redirection and help | FR-M4-07 … FR-M4-09 |

### 2.3 Actors and user classes

| Actor | `Role` | Description | Main use cases |
|---|---|---|---|
| Student | `STUDENT` | Undergraduate or graduate student working on a course or graduation project; registers with a school e-mail. Uses the system a few times per week, mostly before lab work. | UC-1, UC-2, UC-3, UC-4, UC-5, UC-6, UC-8 |
| Instructor | `INSTRUCTOR` | Faculty member; defines office hours, sees topics in advance, grants lab approval, redirects appointments. | UC-1, UC-2, UC-3, UC-4, UC-5 |
| Research Assistant | `RESEARCH_ASSISTANT` | Runs support and training hours, manages the device catalogue, approves lab requests, hands devices over, puts devices into maintenance. | UC-1, UC-2, UC-3, UC-5, UC-6 |
| Department Secretary | `SECRETARY` | Cancels office hours on behalf of instructors, views usage reports. | UC-4, UC-7 |
| Admin | `ADMIN` | Assigns staff roles, views usage reports; may act as a research assistant on catalogue and lab requests. | UC-7, UC-8 |
| SMTP service | – (external system) | Delivers status-change e-mails (US-23). | UC-2, UC-3, UC-4, UC-5, UC-6 |

Self-registration creates only `STUDENT` accounts; staff roles are assigned by an admin (FR-M1-05). Seed data provides the first admin.

### 2.4 Operating environment

- Desktop web browser (current Chrome, Edge, Firefox, Safari); English user interface. A mobile layout is not required.
- Server side: Docker with Docker Compose v2; services `web`, `api`, `db`, `mailpit` as in [`docker-compose.yml`](../../docker-compose.yml).
- Ports in development: Web App 5173, API 8080, PostgreSQL 5432, Mailpit SMTP 1025 and web UI 8025.

### 2.5 Design and implementation constraints

| # | Constraint | Source |
|---|---|---|
| K1 | The whole system starts with one command: `docker compose up`. | Course, vision §3.4 |
| K2 | API-first: every endpoint is defined in `openapi.yaml` before it is implemented; the contract is the single source of truth. A field or endpoint not in the contract is first added there (Mürsel, new version). | Course, ADR-0001 |
| K3 | Lab approval and training conditions are enforced on the server; hiding a button in the UI is not enough. | Vision §3.4 |
| K4 | Double booking is prevented by a PostgreSQL `EXCLUDE USING gist` constraint on active reservations, not by application code alone. | ADR-0001 |
| K5 | Stack: .NET 10, EF Core 10, PostgreSQL 17, React + Vite + JavaScript + Ant Design. One UI library only. | ADR-0001 |
| K6 | Personal data is limited to full name, school e-mail and role (KVKK data minimisation). Only synthetic test data is used. | Vision §3.4, proposal §2.2 |
| K7 | Git-Flow, pull requests with review, ≥ 50% unit-test coverage enforced in CI (from Sprint 3), SonarQube analysis. | Course, [`git-flow.md`](../sprint1/git-flow.md) |
| K8 | MIT license; all dependencies must be MIT-compatible. | Proposal §2.4 |
| K9 | UI, documentation, code, Issues, PRs and commit messages are in English. | Proposal §2.2 |

### 2.6 Assumptions and dependencies

| # | Assumption / dependency |
|---|---|
| A1 | One university, one department; authentication with school e-mail and password (no SSO). The allowed e-mail domain is a configuration value. |
| A2 | Lab approval is granted by an instructor of the student's course or project, per device category, with an end date. |
| A3 | Device training is per category and valid until the end of the current semester (`validUntil`). |
| A4 | Office-hour appointments are approved automatically when the slot is free; lab reservations are approved by a research assistant. |
| A5 | Users have a modern desktop browser and access to their school mailbox. |
| A6 | The SMTP service is available in production; in development Mailpit captures all mail. E-mail failure never blocks a booking (FR-M1-10). |
| A7 | Server clocks are synchronised. Timestamps are stored in UTC; every "today", "same day" or date comparison uses the calendar date in Europe/Istanbul (FR-M3-05, FR-M4-04, FR-M4-07). |

### 2.7 User evidence (student survey)

The survey in [`survey.md`](../sprint1/survey.md) is the primary evidence for the problem statement. Results are reported with the analysis plan of the survey. They come from a small convenience sample and are directional evidence only, read together with the published studies in [proposal §1.2](../sprint1/proposal.md).

**Status: pending – the survey is still open.** The Result column is filled from the Google Forms summary when the survey closes; until then no figure is reported.

Responses: **n = pending – survey still open** (target 30+).

| Output | Question | Result | Requirement it supports |
|---|---|---|---|
| Share of students who never attend office hours | Q3 | Pending – survey still open | FR-M4-01 … FR-M4-05 (bookable office hours) |
| Most frequent reasons for not attending (top 3) | Q4 | Pending – survey still open | FR-M4-07 (timely cancellation), FR-M1-07 (notifications) |
| Value of knowing the topic in advance (mean, 1–5) | Q5 | Pending – survey still open | FR-M4-03 (mandatory topic note) |
| Lab conflicts / untrained use / lack of information | Q7 | Pending – survey still open | FR-M2-04 (no double booking), FR-M2-03 (eligibility), FR-M3-03 (catalogue) |
| Slowness of the approval process (took several days or gave up) | Q8 | Pending – survey still open | FR-M3-04 … FR-M3-06 (lab approval in the system) |
| Ease of reaching the assistant (mean, 1–5) | Q9 | Pending – survey still open | FR-M4-09 (help appointment) |
| Need for the solution (mean; share of 4–5) | Q10 | Pending – survey still open | Whole product |

---

## 3. Functional requirements

Every FR is linked to exactly one user story; every user story has at least one FR (Section 8.2). Priority and sprint come from the [Sprint 2 backlog](backlog.md).

### 3.1 M1 Identity

| ID | Requirement | Story | Priority | Sprint |
|---|---|---|---|---|
| FR-M1-01 | The system shall register a new account with role `STUDENT` from `email`, `password` (8–128 characters) and `fullName` (2–100 characters). An e-mail outside the configured school domain shall be rejected with `400 EMAIL_DOMAIN_NOT_ALLOWED`; other invalid fields with `400 VALIDATION_ERROR`. On success it shall return `201` with the created `User`. | US-01 | Must | 3 |
| FR-M1-02 | The system shall reject a registration whose e-mail is already registered (case-insensitive) with `409 EMAIL_ALREADY_REGISTERED`. | US-01 | Must | 3 |
| FR-M1-03 | The system shall authenticate a user with e-mail and password and return `200` with a `TokenResponse` (Bearer JWT, `expiresIn`, `User` with role). Wrong credentials shall return `401 UNAUTHORIZED` without telling which field was wrong. | US-02 | Must | 3 |
| FR-M1-04 | The system shall enforce role-based access on every endpoint: a missing, invalid or expired token returns `401 UNAUTHORIZED`; a valid token with a role that is not allowed returns `403 FORBIDDEN`. `GET /users/me` shall return the caller's `User` so the Web App can route to the role's home page. | US-02 | Must | 3 |
| FR-M1-05 | The system shall let an `ADMIN` change the role of a user with `PUT /admin/users/{userId}/role`. A value outside `Role` returns `400 VALIDATION_ERROR`, an unknown user `404 NOT_FOUND`, any other caller `403 FORBIDDEN`. | US-03 | Should | Final |
| FR-M1-06 | The system shall record every role change in an audit log with the target user, old role, new role, the admin who made the change and the time (`AuditLog`, table `identity.audit_log`). | US-03 | Should | Final |
| FR-M1-07 | The system shall create an in-app `Notification` for the affected user on these events: reservation approved (`RESERVATION_APPROVED`), rejected (`RESERVATION_REJECTED`), cancelled by someone else or by maintenance (`RESERVATION_CANCELLED`), office hour cancelled (`OFFICE_HOUR_CANCELLED`), appointment redirected (`APPOINTMENT_REDIRECTED`), training granted (`TRAINING_GRANTED`). A failure to create a notification shall be logged and shall not roll back the business operation. | US-04 | Must | 3 |
| FR-M1-08 | `GET /notifications` shall return only the caller's notifications, newest first, each with `type`, `message`, related `reservationId`, `createdAt` and `read`. | US-04 | Must | 3 |
| FR-M1-09 | `GET /reports/usage?from=&to=` shall return, for the date range, per resource the `utilisationRate` (0–1), `reservationCount` and `cancellationCount`, and per schedule the `slotCount`, `bookedCount` and `cancelledCount`. Only `SECRETARY` and `ADMIN` may call it (otherwise `403 FORBIDDEN`); `from` after `to` returns `400 VALIDATION_ERROR`. | US-05 | Should | Final |
| FR-M1-10 | For every event in FR-M1-07 the system shall also send an e-mail to the affected user through the SMTP service (Mailpit in development). A send failure shall be logged and shall not roll back the business operation or the in-app notification. | US-23 | Should | Final |

### 3.2 M2 Booking Core

| ID | Requirement | Story | Priority | Sprint |
|---|---|---|---|---|
| FR-M2-01 | The system shall let a `STUDENT` request a lab reservation with `POST /reservations` (`resourceId`, `startsAt`, `endsAt`, optional `purpose` ≤ 500 characters). `endsAt` not later than `startsAt`, or `startsAt` earlier than now, shall be rejected with `400 INVALID_INTERVAL` (C1). | US-06 | Must | 3 |
| FR-M2-02 | An unknown `resourceId` shall return `404 NOT_FOUND`; a resource whose status is `MAINTENANCE` shall return `409 RESOURCE_IN_MAINTENANCE`. | US-06 | Must | 3 |
| FR-M2-03 | Before creating a reservation, M2 shall call the `IEligibilityPolicy` of the resource type. For lab devices (policy provided by M3) the student needs an active lab approval for the device category, otherwise `403 LAB_APPROVAL_REQUIRED`, and a valid training authorisation for the category, otherwise `403 TRAINING_REQUIRED`. | US-06 | Must | 3 |
| FR-M2-04 | The system shall reject a reservation that overlaps a `PENDING`, `APPROVED` or `CHECKED_IN` reservation of the same resource with `409 SLOT_UNAVAILABLE`. The guarantee shall hold under concurrent requests: the database exclusion constraint (SQLSTATE `23P01`) is translated into the same `409`. | US-06 | Must | 3 |
| FR-M2-05 | On success the system shall return `201` with the `Reservation` in status `PENDING` and a `Location` header, and write the creation (`from = null`, `to = PENDING`) to the status history. The owner, a `RESEARCH_ASSISTANT` or an `ADMIN` may read it with `GET /reservations/{reservationId}`; anyone else gets `403 FORBIDDEN`. | US-06 | Must | 3 |
| FR-M2-06 | The conflict check shall treat intervals as half-open `[startsAt, endsAt)`: back-to-back intervals (10:00–11:00 and 11:00–12:00) do not conflict; partial and nested overlaps do. The checks of FR-M2-01 … FR-M2-04 shall run in the order given in the contract (interval → resource → maintenance → eligibility → conflict). | US-07 | Must | 3 |
| FR-M2-07 | A `RESEARCH_ASSISTANT` or `ADMIN` shall approve (`POST /reservations/{reservationId}/approve`) or reject (`POST /reservations/{reservationId}/reject`, `reason` 5–500 characters, required) a `PENDING` lab reservation. A missing or too short reason returns `400 VALIDATION_ERROR`; other roles `403 FORBIDDEN`. Pending requests are listed with `GET /reservations?kind=LAB&status=PENDING`. | US-08 | Must | 3 |
| FR-M2-08 | The system shall allow only these status transitions: `PENDING` → `APPROVED`, `REJECTED` or `CANCELLED`; `APPROVED` → `CHECKED_IN`, `CANCELLED` or `COMPLETED`; `CHECKED_IN` → `COMPLETED`. Any other transition returns `409 INVALID_STATE_TRANSITION`. | US-08 | Must | 3 |
| FR-M2-09 | Every status transition shall be written to the status history (`from`, `to`, `changedBy`, `changedAt`, `reason`) in the same transaction as the change, and returned by `GET /reservations/{reservationId}/history` in time order. | US-08 | Must | 3 |
| FR-M2-10 | The owner shall cancel their own reservation or appointment with `POST /reservations/{reservationId}/cancel` (optional `reason`) before its `startsAt`; at or after `startsAt` the system returns `409 CANCELLATION_WINDOW_PASSED`, for another user's booking `403 FORBIDDEN`. The status becomes `CANCELLED` and the interval is free for new bookings immediately. Own bookings are listed with `GET /reservations`. | US-09 | Must | 3 |
| FR-M2-11 | `GET /me/calendar?from=&to=` shall return the caller's own appointments, trainings and lab reservations in the range as `CalendarEntry` items, ordered by `startsAt`, each with `kind`, `scheduleType` (for appointments), `title` and `status`. | US-10 | Should | Final |

### 3.3 M3 Lab

| ID | Requirement | Story | Priority | Sprint |
|---|---|---|---|---|
| FR-M3-01 | A `RESEARCH_ASSISTANT` or `ADMIN` shall add a resource with `POST /resources`. `name` (≤ 120), `type` (`DEVICE`, `ROOM`), `category` and `location` are required, otherwise `400 VALIDATION_ERROR`; other roles get `403 FORBIDDEN`. A new resource has status `AVAILABLE`. | US-11 | Must | 3 |
| FR-M3-02 | `serialNumber` is optional and unique when present; a duplicate returns `409 DUPLICATE_SERIAL_NUMBER`. The same rules apply when a resource is edited with `PUT /resources/{resourceId}`. | US-11 | Must | 3 |
| FR-M3-03 | `GET /resources` shall filter the catalogue by `type`, `category` and availability (`availableFrom` + `availableTo`: only resources free for the whole interval), with all given filters applied together (AND). No match returns `200` with an empty list. `GET /resources/{resourceId}` returns one resource or `404 NOT_FOUND`. | US-12 | Must | 3 |
| FR-M3-04 | An `INSTRUCTOR` shall grant lab access with `POST /lab-approvals` (`appointmentId`, `deviceCategory`, `expiresOn`; all required, otherwise `400 VALIDATION_ERROR`). The appointment must be an `OFFICE_HOUR` appointment hosted by the caller, in status `APPROVED` or `COMPLETED` (appointments are completed automatically after their end time, ADR-0003), otherwise `403 FORBIDDEN`; an unknown appointment returns `404 NOT_FOUND`. The student is taken from the appointment, and the approval is linked to it. | US-13 | Must | 3 |
| FR-M3-05 | A lab approval shall be active until the end of its `expiresOn` date (date in Europe/Istanbul). After that it shall be reported with `active = false` and shall not satisfy the eligibility check of FR-M2-03. `expiresOn` earlier than today (Europe/Istanbul) shall be rejected with `400 VALIDATION_ERROR`. | US-13 | Must | 3 |
| FR-M3-06 | `GET /lab-approvals` shall return all approvals to a `RESEARCH_ASSISTANT` or `ADMIN` (lab panel), filterable by `studentId`, `deviceCategory` and `activeOnly` (default `true`). A student sees only their own approvals, an instructor only the ones they granted. | US-13 | Must | 3 |
| FR-M3-07 | A `RESEARCH_ASSISTANT` shall complete a `TRAINING` appointment hosted by the caller with `POST /training-authorizations` (`appointmentId`). In one transaction the appointment becomes `COMPLETED` and a `TrainingAuthorization` for the training's device category is created, valid until the end of the current semester: `validUntil` is the configuration value `Semester__EndDate`. A non-training appointment returns `400 NOT_A_TRAINING_APPOINTMENT`; an existing authorisation of the same student for the same category returns `409 TRAINING_ALREADY_GRANTED`. | US-14 | Must | 3 |
| FR-M3-08 | When an authorisation is created, the instructor(s) who granted an active lab approval to that student for the same category shall receive a `TRAINING_GRANTED` notification (FR-M1-07). `GET /training-authorizations` lists authorisations with the same visibility rules as FR-M3-06. | US-14 | Must | 3 |
| FR-M3-09 | A `RESEARCH_ASSISTANT` shall issue a one-time 6-digit handover code for an `APPROVED` lab reservation. The code expires after the configuration value `Handover__CodeLifetimeMinutes` (default 15 minutes) (`POST /reservations/{reservationId}/handover-code`). For any other status the system returns `409 INVALID_STATE_TRANSITION`. | US-15 | Should | Final |
| FR-M3-10 | `POST /reservations/{reservationId}/check-in` with the correct, unused, unexpired code shall set the reservation to `CHECKED_IN`; a wrong, used or expired code returns `400 INVALID_HANDOVER_CODE`. `POST /reservations/{reservationId}/return` on a `CHECKED_IN` reservation shall set it to `COMPLETED`. | US-15 | Should | Final |
| FR-M3-11 | A `RESEARCH_ASSISTANT` or `ADMIN` shall put a resource into maintenance with `POST /resources/{resourceId}/maintenance` (`reason` 5–500 characters). The resource status becomes `MAINTENANCE`; all its future `PENDING` and `APPROVED` reservations become `CANCELLED`, their owners receive `RESERVATION_CANCELLED` notifications, and the response contains the number of cancelled reservations. A resource already in maintenance returns `409 RESOURCE_IN_MAINTENANCE`. `DELETE /resources/{resourceId}/maintenance` returns it to `AVAILABLE`. | US-16 | Should | Final |

### 3.4 M4 Office Hours

| ID | Requirement | Story | Priority | Sprint |
|---|---|---|---|---|
| FR-M4-01 | A host shall define a weekly schedule with `POST /office-hours` (`type`, `dayOfWeek`, `startTime`, `endTime`, `slotMinutes` 10–120, `location`; `deviceCategory` required when `type` is `TRAINING`). An `INSTRUCTOR` may define only `OFFICE_HOUR`, a `RESEARCH_ASSISTANT` only `SUPPORT` and `TRAINING`; any other combination returns `403 FORBIDDEN`. `endTime` not after `startTime` returns `400 VALIDATION_ERROR`. | US-17 | Must | 3 |
| FR-M4-02 | The system shall generate the slots of a schedule from `slotMinutes` and reject a schedule that overlaps another active schedule of the same host on the same day with `409 SCHEDULE_OVERLAP`. Schedules are listed with `GET /office-hours` (filters `hostId`, `type`, `deviceCategory`) and slots with `GET /office-hours/{scheduleId}/slots?from=&to=` (`onlyAvailable` optional). | US-17 | Must | 3 |
| FR-M4-03 | A `STUDENT` shall book a slot with `POST /appointments` (`slotId`, `topic` of 10–500 characters, required). A topic that is missing, blank or outside the length returns `400 VALIDATION_ERROR`. The appointment is created through `IBookingEngine` as an M2 reservation of kind `APPOINTMENT` and is `APPROVED` immediately (`201`). | US-18 | Must | 3 |
| FR-M4-04 | Booking a slot that is already taken returns `409 SLOT_UNAVAILABLE`; a second appointment of the same student with the same host on the same day (date in Europe/Istanbul) returns `409 DAILY_APPOINTMENT_LIMIT`, where only `APPROVED` appointments count (a `CANCELLED` one does not); a slot whose start time has passed returns `400 INVALID_INTERVAL` (C1). | US-18 | Must | 3 |
| FR-M4-05 | `GET /appointments/my` shall return the calling student's own appointments, filterable by `from`, `to` and `status`, with host name, topic, location and status. | US-18 | Must | 3 |
| FR-M4-06 | `GET /appointments/incoming?date=` shall return the appointments of the caller's own schedules (`INSTRUCTOR` or `RESEARCH_ASSISTANT`) for that day, in time order, with student name and topic; cancelled appointments are included and marked by `status`. Other roles get `403 FORBIDDEN`. | US-19 | Must | 3 |
| FR-M4-07 | A `SECRETARY`, an `ADMIN` or the schedule's host shall cancel a schedule for one date (date in Europe/Istanbul) with `POST /office-hours/{scheduleId}/cancellations` (`date`, `reason` 5–500 characters, required). A date before today returns `400 VALIDATION_ERROR`; today is allowed. All `APPROVED` appointments of that schedule on that date that have not started yet become `CANCELLED`, each affected student receives an `OFFICE_HOUR_CANCELLED` notification, and the response contains the numbers of cancelled appointments and notified students. Any other caller gets `403 FORBIDDEN`. | US-20 | Must | 3 |
| FR-M4-08 | An `INSTRUCTOR` shall redirect an appointment of their own schedule to a free slot of a `SUPPORT` schedule with `POST /appointments/{appointmentId}/redirect` (`targetSlotId`, optional `note` ≤ 500). The old appointment becomes `CANCELLED`, a new appointment with the same topic is created (`201`), and the student receives an `APPOINTMENT_REDIRECTED` notification. A target slot that is not free returns `409 NO_AVAILABLE_SLOT`. | US-21 | Should | Final |
| FR-M4-09 | A `STUDENT` shall open a help appointment in a `SUPPORT` slot with `POST /appointments` and `relatedReservationId`. The referenced reservation must be the student's own lab reservation in status `APPROVED` or `CHECKED_IN`, otherwise `403 FORBIDDEN`; its resource and time are attached to the appointment automatically. | US-22 | Should | Final |

### 3.5 Summary

| Module | FRs | Must (Sprint 3) | Should (Final) |
|---|:-:|:-:|:-:|
| M1 Identity | 10 | 6 | 4 |
| M2 Booking Core | 11 | 10 | 1 |
| M3 Lab | 11 | 8 | 3 |
| M4 Office Hours | 9 | 7 | 2 |
| **Total** | **41** | **31** | **10** |

---

## 4. Use case descriptions

The use cases are the ones defined in [`vision.md` §3.5](../sprint1/vision.md); their diagram is in [`uml.md`](../architecture/uml.md). Error codes in the alternative flows are the RFC 9457 `code` values of the contract.

### UC-1 Get lab access approval

| Field | Description |
|---|---|
| Actors | Student (primary), Instructor, Research Assistant |
| Stories / FRs | US-18, US-19, US-13 · FR-M4-03 … FR-M4-06, FR-M3-04 … FR-M3-06 |
| Screens | SCR-04 → SCR-05 → SCR-08 |
| Preconditions | The student is signed in. The instructor has an active `OFFICE_HOUR` schedule with free slots (UC-2 step 1 / US-17). The device category exists in the catalogue. |
| Main flow | 1. The student opens the instructor's office hours and selects a free slot. 2. The student writes the topic (e.g. "I need an oscilloscope for my motor driver project") and books. 3. The system creates the appointment as `APPROVED`. 4. On the meeting day, the instructor opens the day's appointments and reads the topic. 5. After the meeting, the instructor selects the appointment, enters the device category and an end date and grants lab approval. 6. The system stores the approval linked to the appointment. 7. The research assistant sees the approval in the lab panel. |
| Alternative flows | 2a. Topic shorter than 10 or longer than 500 characters → `400 VALIDATION_ERROR`; the student corrects it. 2b. Slot taken meanwhile → `409 SLOT_UNAVAILABLE`; the student picks another slot. 2c. Student already has an appointment with this instructor that day → `409 DAILY_APPOINTMENT_LIMIT`. 2d. Slot already started → `400 INVALID_INTERVAL`. 5a. Category or end date missing, or end date in the past → `400 VALIDATION_ERROR`. 5b. Appointment not hosted by this instructor or not an office-hour appointment → `403 FORBIDDEN`. 5c. Instructor decides no lab use is needed → no approval; the use case ends. |
| Postconditions | An active `LabApproval` exists for (student, category) until `expiresOn`; it is visible in the lab panel and satisfies the approval half of the eligibility check (FR-M2-03). |

### UC-2 Complete device training

| Field | Description |
|---|---|
| Actors | Student (primary), Research Assistant, Instructor |
| Stories / FRs | US-17, US-18, US-14, US-04 · FR-M4-01, FR-M4-02, FR-M4-03, FR-M4-04, FR-M3-07, FR-M3-08, FR-M1-07, FR-M1-08 |
| Screens | SCR-03 → SCR-06 → CMP-01 |
| Preconditions | The research assistant has defined a `TRAINING` schedule for the device category. The student is signed in. Usually the student already holds a lab approval (UC-1); this is not required to book the training. |
| Main flow | 1. The research assistant defines a weekly training hour for the category; the system generates the slots. 2. The student selects a training slot of the category and books it with a topic note. 3. After the session, the research assistant opens the day's training appointments and selects "Complete training". 4. The system marks the appointment `COMPLETED` and creates a training authorisation for the category valid until the end of the semester. 5. The system notifies the instructor who granted the student's lab approval for this category (`TRAINING_GRANTED`). 6. The student sees the authorisation under "My authorizations". |
| Alternative flows | 1a. Training hour overlaps another schedule of the same research assistant → `409 SCHEDULE_OVERLAP`. 1b. `deviceCategory` missing for `TRAINING` → `400 VALIDATION_ERROR`. 2a. Booking errors as in UC-1 2a–2d. 3a. The selected appointment is not a training appointment → `400 NOT_A_TRAINING_APPOINTMENT`. 3b. Student already authorised for the category → `409 TRAINING_ALREADY_GRANTED`. 5a. No instructor approval exists for the category → no instructor notification; the authorisation is still created. 5b. Notification fails → logged; the authorisation is kept. |
| Postconditions | A `TrainingAuthorization` exists for (student, category); the training appointment is `COMPLETED`; the approving instructor has a notification (and an e-mail, Should). |

### UC-3 Reserve and check out a lab device

| Field | Description |
|---|---|
| Actors | Student (primary), Research Assistant, Instructor |
| Stories / FRs | US-11, US-12, US-06, US-07, US-08, US-09, US-10, US-15, US-22, US-04 · FR-M3-01 … FR-M3-03, FR-M2-01 … FR-M2-11, FR-M3-09, FR-M3-10, FR-M4-09, FR-M1-07 |
| Screens | SCR-11 → SCR-07 → SCR-08 → SCR-02 (help: SCR-10) |
| Preconditions | The device is in the catalogue and `AVAILABLE` (US-11). The student holds an active lab approval and a training authorisation for the device's category (UC-1, UC-2). |
| Main flow | 1. The student filters the catalogue by type, category and availability. 2. The student selects a device and a time range and submits a request with a purpose. 3. The system checks interval, resource, maintenance, eligibility and conflicts and creates the reservation as `PENDING`. 4. The research assistant lists pending requests and approves the request. 5. The system sets it to `APPROVED`, records the transition and notifies the student. 6. *(Should)* At the start time the research assistant issues a handover code; the student shows it; the research assistant enters it and the reservation becomes `CHECKED_IN`. 7. *(Should)* On return the research assistant records the return; the reservation becomes `COMPLETED`. |
| Alternative flows | 2a. End not after start, or start in the past → `400 INVALID_INTERVAL`. 3a. Device in maintenance → `409 RESOURCE_IN_MAINTENANCE`. 3b. No active approval → `403 LAB_APPROVAL_REQUIRED`; the UI links to SCR-04 (UC-1). 3c. No training authorisation → `403 TRAINING_REQUIRED`; the UI links to SCR-06 (UC-2). 3d. Overlap with a pending, approved or checked-in reservation → `409 SLOT_UNAVAILABLE` (back-to-back is allowed). 4a. The research assistant rejects with a reason (5–500 characters) → `REJECTED`; the student is notified. 4b. The request was already handled → `409 INVALID_STATE_TRANSITION`. 4c. The student cancels before the start time → `CANCELLED`, interval freed; after the start → `409 CANCELLATION_WINDOW_PASSED`. 6a. Wrong, used or expired code → `400 INVALID_HANDOVER_CODE`. 6b. The student gets stuck during use and opens a help appointment in a support hour from the reservation (US-22) → appointment with `relatedReservationId`. |
| Postconditions | The reservation is `APPROVED` (MVP) or `COMPLETED` (with handover); every transition is in the status history; the student sees it in the calendar (Should). |

### UC-4 Cancel office hours

| Field | Description |
|---|---|
| Actors | Department Secretary (primary), Instructor, Student; SMTP service (Should) |
| Stories / FRs | US-20, US-04, US-23 · FR-M4-07, FR-M1-07, FR-M1-08, FR-M1-10 |
| Screens | SCR-09 → CMP-01, SCR-02 |
| Preconditions | The schedule exists and has appointments on the given date. The instructor has told the secretary that they cannot come in (or cancels by themselves). |
| Main flow | 1. The secretary selects the instructor's schedule and the date. 2. The system shows how many appointments will be affected. 3. The secretary enters the reason and confirms. 4. The system sets all `APPROVED` appointments of that schedule on that date that have not started yet to `CANCELLED`. 5. Each affected student receives an `OFFICE_HOUR_CANCELLED` notification (and an e-mail, Should). 6. The system returns the numbers of cancelled appointments and notified students. |
| Alternative flows | 1a. The instructor cancels their own schedule instead of the secretary → same flow. 3a. Reason missing or shorter than 5 characters → `400 VALIDATION_ERROR`. 3b. Caller is neither secretary, admin nor host → `403 FORBIDDEN`. 3c. Date before today → `400 VALIDATION_ERROR`; for today only appointments that have not started yet are cancelled. 4a. No appointments on that date → `200` with zero counts. 5a. Notification or e-mail failure → logged; the cancellation is kept. |
| Postconditions | No active appointment remains on that schedule and date; every affected student has been notified. |

### UC-5 Redirect an appointment to the assistant

| Field | Description |
|---|---|
| Actors | Instructor (primary), Research Assistant, Student |
| Stories / FRs | US-21, US-04 · FR-M4-08, FR-M1-07 |
| Screens | SCR-05 → SCR-10 → SCR-02 |
| Preconditions | The appointment belongs to the instructor's own office hour and is `APPROVED`. A research assistant has a `SUPPORT` schedule with a free slot. |
| Main flow | 1. The instructor reads the topic and decides the question is better answered by the research assistant (e.g. homework or lab usage). 2. The instructor selects "Redirect to assistant", picks a free support slot and adds an optional note. 3. The system cancels the old appointment and creates a new appointment in the support slot with the same topic. 4. The student receives an `APPOINTMENT_REDIRECTED` notification and sees the new appointment. 5. The research assistant sees it in the day's appointments. |
| Alternative flows | 2a. The target slot is no longer free → `409 NO_AVAILABLE_SLOT`; the instructor picks another slot. 2b. Appointment not in the instructor's own schedule → `403 FORBIDDEN`. |
| Postconditions | The old appointment is `CANCELLED`, the new one `APPROVED`; both transitions are in the status history. |

### UC-6 Put a device under maintenance *(supporting)*

| Field | Description |
|---|---|
| Actors | Research Assistant (primary), Student |
| Stories / FRs | US-16, US-04 · FR-M3-11, FR-M2-02, FR-M1-07 |
| Screens | SCR-08 → CMP-01 |
| Preconditions | The device exists and is `AVAILABLE`. |
| Main flow | 1. The research assistant opens the device and selects "Start maintenance". 2. The system warns how many future reservations will be cancelled. 3. The research assistant enters a reason and confirms. 4. The system sets the device to `MAINTENANCE` and cancels its future `PENDING` and `APPROVED` reservations. 5. Each owner receives a `RESERVATION_CANCELLED` notification. 6. When the repair is done, the research assistant ends maintenance; the device is `AVAILABLE` again. |
| Alternative flows | 3a. Reason missing or shorter than 5 characters → `400 VALIDATION_ERROR`. 4a. Device already in maintenance → `409 RESOURCE_IN_MAINTENANCE`. While in maintenance, a new request → `409 RESOURCE_IN_MAINTENANCE`. |
| Postconditions | No future active reservation exists for the device while it is in maintenance; affected students are notified. |

### UC-7 Report usage *(supporting)*

| Field | Description |
|---|---|
| Actors | Department Secretary (primary), Admin |
| Stories / FRs | US-05 · FR-M1-09 |
| Screens | SCR-09 |
| Preconditions | The caller is `SECRETARY` or `ADMIN`. |
| Main flow | 1. The user selects a date range. 2. The system returns per-device utilisation, reservation and cancellation counts, and per-schedule slot, booked and cancelled counts. 3. The user reads the report on screen. |
| Alternative flows | 1a. `from` after `to` → `400 VALIDATION_ERROR`. 1b. No data in the range → `200` with zero values. 1c. Other role → `403 FORBIDDEN`. |
| Postconditions | None (read-only). |

### UC-8 Account and role management *(supporting)*

| Field | Description |
|---|---|
| Actors | Student (primary), Admin |
| Stories / FRs | US-01, US-02, US-03 · FR-M1-01 … FR-M1-06 |
| Screens | SCR-01, SCR-09 |
| Preconditions | Self-registration: none. Role assignment: an admin account exists (seed data). |
| Main flow | 1. The student registers with school e-mail, full name and password. 2. The system creates a `STUDENT` account. 3. The user signs in and receives a JWT; the Web App reads the role and opens the role's home page. 4. *(Should)* For a new staff member, the admin assigns the role (`INSTRUCTOR`, `RESEARCH_ASSISTANT`, `SECRETARY` or `ADMIN`); the change is written to the audit log. |
| Alternative flows | 1a. E-mail outside the school domain → `400 EMAIL_DOMAIN_NOT_ALLOWED`. 1b. E-mail already registered → `409 EMAIL_ALREADY_REGISTERED`. 3a. Wrong credentials → `401 UNAUTHORIZED`. 3b. Opening a screen of another role → `403 FORBIDDEN` page. 4a. Unknown role value → `400 VALIDATION_ERROR`. |
| Postconditions | The user exists with exactly one role; every role change is auditable. |

**Story coverage.** Each user story appears in at least one use case above. US-07 (unit tests) supports UC-3 through FR-M2-06; US-23 (e-mail) supports every use case that sends notifications and is listed in UC-4, where the SMTP service is an actor.

---

## 5. Data requirements

The data model has one entity per OpenAPI schema below. Tables, columns, keys, the `EXCLUDE USING gist` constraint and indexes are specified in [`database.md`](../architecture/database.md); the class diagram is in [`uml.md`](../architecture/uml.md).

| Entity (OpenAPI) | Table | Owner module | Key attributes | Rules from this SRS |
|---|---|---|---|---|
| `User` | `users` | M1 | `id`, `email` (unique), `fullName`, `role`, password hash | FR-M1-01 … FR-M1-05; only name, e-mail and role are personal data (K6) |
| `Notification` | `notifications` | M1 | `id`, user, `type`, `message`, `reservationId`, `createdAt`, `read` | FR-M1-07, FR-M1-08 |
| `AuditLog` (not in the API) | `audit_log` | M1 | target user, old role, new role, changed by, changed at | FR-M1-06 (C2) |
| `Reservation` | `reservations` | M2 | `id`, `kind`, `resourceId`, `ownerId`, `startsAt`, `endsAt`, `purpose`, `status`, `createdAt` | FR-M2-01 … FR-M2-10; no overlap among `PENDING`, `APPROVED`, `CHECKED_IN` per resource (K4) |
| `StatusChange` | `status_changes` | M2 | reservation, `from`, `to`, `changedBy`, `changedAt`, `reason` | FR-M2-09; written in the same transaction as the change |
| `Resource` | `resources` | M3 | `id`, `name`, `type`, `category`, `location`, `serialNumber` (unique when present), `status` | FR-M3-01 … FR-M3-03, FR-M3-11 |
| `LabApproval` | `lab_approvals` | M3 | `id`, `studentId`, `instructorId`, `appointmentId`, `deviceCategory`, `expiresOn`, `createdAt` (`active` derived) | FR-M3-04 … FR-M3-06 |
| `HandoverCode` | `handover_codes` | M3 | reservation, 6-digit code, `expiresAt`, used | FR-M3-09, FR-M3-10 |
| `TrainingAuthorization` | `training_authorizations` | M3 | `id`, `studentId`, `deviceCategory`, `grantedBy`, `appointmentId`, `validUntil` | FR-M3-07, FR-M3-08; unique (student, category) |
| `OfficeHourSchedule` | `office_hour_schedules` | M4 | `id`, `hostId`, `type`, `dayOfWeek`, `startTime`, `endTime`, `slotMinutes`, `location`, `deviceCategory`, `active` | FR-M4-01, FR-M4-02 |
| `Slot` | `slots` | M4 | `id`, `scheduleId`, `hostId`, `type`, `startsAt`, `endsAt`, `available` | FR-M4-02, FR-M4-04 |
| `Appointment` | `appointments` | M4 | `id` (= M2 reservation id), `slotId`, `scheduleType`, `hostId`, `studentId`, `topic`, `relatedReservationId`, `status` | FR-M4-03 … FR-M4-09 |

Additional data rules:

- **Retention and deletion.** Only synthetic data is used in this project. Users and bookings are not hard-deleted; status history is kept for audit.
- **Module schemas.** Each module owns one PostgreSQL schema (`identity`, `booking`, `lab`, `office_hours`) and is the only one that reads or writes it ([`database.md`](../architecture/database.md)).
- **Time zone.** Timestamps are stored with offset (`timestamptz`); intervals as `tstzrange` `[startsAt, endsAt)`.

---

## 6. External interface requirements

### 6.1 User interfaces

- 11 screens and the notification center, listed with their roles, stories and endpoints in [`ui-screens.md`](../ui-screens.md); clickable prototypes and user flows in [`ui-prototypes.md`](ui-prototypes.md).
- Every list screen has loading, empty, error and populated states. Server errors are shown as English messages chosen by the `code` field ([`ui-screens.md` §8](../ui-screens.md)).
- The menu shows only the screens of the user's role; an unauthorised URL shows a 403 page, a missing token redirects to SCR-01.

### 6.2 Software interfaces (REST API)

- The REST API is specified in [`openapi.yaml`](../api/openapi.yaml) (OpenAPI 3.1, v0.2.2; v0.2.3 pending for C1): 35 operations, every one linked to a story by `x-story` ([story mapping](../api/story-mapping.md)).
- JSON over HTTP, base path `/api/v1`, JWT Bearer authentication.
- Errors: `application/problem+json` (RFC 9457) with `title`, `status`, `code`, `detail` and optional field `errors`.
- During frontend development the contract is served by the Prism mock server.

### 6.3 Communication interfaces

- **SMTP:** the API sends status-change e-mails through MailKit. In development the `mailpit` service receives them on port 1025 and shows them at http://localhost:8025 (US-23).
- **Database:** the API talks to PostgreSQL 17 (`db` service) over the internal Docker network; the database is inside the system boundary.
- No other external system is connected in this release (no SSO, no calendar sync).

### 6.4 Hardware interfaces

None. Handover uses a code typed by the research assistant; no card reader, lock or IoT device is connected.

---

## 7. Non-functional requirements

Non-functional requirements are owned by Mirac and specified in [`nfr.md`](nfr.md). Each NFR has an ID (`NFR-<category>-<no>`; categories PERF, SEC, REL, USE, MAIN, PORT, PRIV), a measurable target, a measurement method and the FR/US it applies to. The test cases that verify them are in the [test plan](../test-plan.md).

Constraints from Sprint 1 that the NFRs make measurable include: catalogue response time p95 < 200 ms on 500 records (US-12, FR-M3-03), no double booking under concurrency (FR-M2-04), RFC 9457 errors, ≥ 50% coverage in CI, KVKK data minimisation (K6) and single-command setup (K1).

---

## 8. Traceability

### 8.1 Traceability matrix: FR → US → UC → endpoint → screen

Endpoints are written as in the contract; screens as in [`ui-screens.md`](../ui-screens.md). "Server-side" means the requirement is realised inside an endpoint listed in another row and has no screen of its own.

| FR | US | UC | Endpoint(s) | Screen(s) |
|---|---|---|---|---|
| FR-M1-01 | US-01 | UC-8 | `POST /auth/register` | SCR-01 |
| FR-M1-02 | US-01 | UC-8 | `POST /auth/register` | SCR-01 |
| FR-M1-03 | US-02 | UC-8 | `POST /auth/login` | SCR-01 |
| FR-M1-04 | US-02 | UC-8 | `GET /users/me` (and every protected endpoint) | SCR-01 (role routing, 403 page) |
| FR-M1-05 | US-03 | UC-8 | `PUT /admin/users/{userId}/role` | SCR-09 |
| FR-M1-06 | US-03 | UC-8 | `PUT /admin/users/{userId}/role` | SCR-09 |
| FR-M1-07 | US-04 | UC-2, UC-3, UC-4, UC-5, UC-6 | Server-side, triggered by `approveReservation`, `rejectReservation`, `cancelOfficeHourDay`, `redirectAppointment`, `grantTrainingAuthorization`, `startMaintenance` | CMP-01 |
| FR-M1-08 | US-04 | UC-2, UC-3, UC-4, UC-5, UC-6 | `GET /notifications` | CMP-01 |
| FR-M1-09 | US-05 | UC-7 | `GET /reports/usage` | SCR-09 |
| FR-M1-10 | US-23 | UC-4 (and UC-2, UC-3, UC-5, UC-6) | No endpoint: SMTP e-mail from the same events as FR-M1-07 | No screen: e-mail in the user's mailbox (Mailpit in development) |
| FR-M2-01 | US-06 | UC-3 | `POST /reservations` | SCR-07 |
| FR-M2-02 | US-06 | UC-3, UC-6 | `POST /reservations` | SCR-07 |
| FR-M2-03 | US-06 | UC-1, UC-2, UC-3 | `POST /reservations` | SCR-07 |
| FR-M2-04 | US-06 | UC-3 | `POST /reservations` | SCR-07 |
| FR-M2-05 | US-06 | UC-3 | `POST /reservations`, `GET /reservations/{reservationId}` | SCR-07, SCR-02 |
| FR-M2-06 | US-07 | UC-3 | `POST /reservations` (verified by unit and integration tests) | SCR-07 |
| FR-M2-07 | US-08 | UC-3 | `GET /reservations`, `POST /reservations/{reservationId}/approve`, `POST /reservations/{reservationId}/reject` | SCR-08 |
| FR-M2-08 | US-08 | UC-3 | `POST /reservations/{reservationId}/approve`, `/reject`, `/cancel`, `/check-in`, `/return` | SCR-08, SCR-02 |
| FR-M2-09 | US-08 | UC-3 | `GET /reservations/{reservationId}/history` | SCR-08 |
| FR-M2-10 | US-09 | UC-3, UC-1 | `GET /reservations`, `POST /reservations/{reservationId}/cancel` | SCR-02 |
| FR-M2-11 | US-10 | UC-1, UC-2, UC-3 | `GET /me/calendar` | SCR-02 |
| FR-M3-01 | US-11 | UC-3 | `POST /resources` | SCR-11 |
| FR-M3-02 | US-11 | UC-3 | `POST /resources`, `PUT /resources/{resourceId}` | SCR-11 |
| FR-M3-03 | US-12 | UC-3 | `GET /resources`, `GET /resources/{resourceId}` | SCR-07 |
| FR-M3-04 | US-13 | UC-1 | `GET /appointments/incoming`, `POST /lab-approvals` | SCR-05 |
| FR-M3-05 | US-13 | UC-1, UC-3 | `POST /lab-approvals`, `GET /lab-approvals` | SCR-05, SCR-07 |
| FR-M3-06 | US-13 | UC-1 | `GET /lab-approvals` | SCR-08, SCR-07 |
| FR-M3-07 | US-14 | UC-2 | `GET /appointments/incoming`, `POST /training-authorizations` | SCR-06 |
| FR-M3-08 | US-14 | UC-2 | `POST /training-authorizations`, `GET /training-authorizations` | SCR-06, SCR-07, CMP-01 |
| FR-M3-09 | US-15 | UC-3 | `POST /reservations/{reservationId}/handover-code` | SCR-08 |
| FR-M3-10 | US-15 | UC-3 | `POST /reservations/{reservationId}/check-in`, `POST /reservations/{reservationId}/return` | SCR-08 |
| FR-M3-11 | US-16 | UC-6 | `POST /resources/{resourceId}/maintenance`, `DELETE /resources/{resourceId}/maintenance` | SCR-08 |
| FR-M4-01 | US-17 | UC-2, UC-1 | `POST /office-hours` | SCR-03 |
| FR-M4-02 | US-17 | UC-2, UC-1 | `POST /office-hours`, `GET /office-hours`, `GET /office-hours/{scheduleId}/slots` | SCR-03, SCR-04 |
| FR-M4-03 | US-18 | UC-1, UC-2 | `POST /appointments` | SCR-04, SCR-06 |
| FR-M4-04 | US-18 | UC-1, UC-2 | `POST /appointments` | SCR-04, SCR-06 |
| FR-M4-05 | US-18 | UC-1 | `GET /appointments/my` | SCR-02 |
| FR-M4-06 | US-19 | UC-1 | `GET /appointments/incoming` | SCR-05 |
| FR-M4-07 | US-20 | UC-4 | `POST /office-hours/{scheduleId}/cancellations` | SCR-09 |
| FR-M4-08 | US-21 | UC-5 | `POST /appointments/{appointmentId}/redirect` | SCR-10 |
| FR-M4-09 | US-22 | UC-3 | `POST /appointments` (`relatedReservationId`) | SCR-10 |

### 8.2 Reverse check: US → FR

| US | Title | Priority | FRs |
|---|---|---|---|
| US-01 | Register with school e-mail | Must | FR-M1-01, FR-M1-02 |
| US-02 | Log in and role-based access | Must | FR-M1-03, FR-M1-04 |
| US-03 | Assign roles to staff | Should | FR-M1-05, FR-M1-06 |
| US-04 | In-app notifications | Must | FR-M1-07, FR-M1-08 |
| US-05 | Usage report | Should | FR-M1-09 |
| US-06 | Reserve a lab device | Must | FR-M2-01, FR-M2-02, FR-M2-03, FR-M2-04, FR-M2-05 |
| US-07 | Unit tests for the conflict engine | Must | FR-M2-06 |
| US-08 | Approve or reject lab requests | Must | FR-M2-07, FR-M2-08, FR-M2-09 |
| US-09 | Cancel reservation or appointment | Must | FR-M2-10 |
| US-10 | Unified calendar | Should | FR-M2-11 |
| US-11 | Add and edit devices and rooms | Must | FR-M3-01, FR-M3-02 |
| US-12 | Filter the catalogue | Must | FR-M3-03 |
| US-13 | Lab access approval from office hour | Must | FR-M3-04, FR-M3-05, FR-M3-06 |
| US-14 | Device training and authorisation | Must | FR-M3-07, FR-M3-08 |
| US-15 | Handover code and return | Should | FR-M3-09, FR-M3-10 |
| US-16 | Put a device into maintenance | Should | FR-M3-11 |
| US-17 | Define office, support and training hours | Must | FR-M4-01, FR-M4-02 |
| US-18 | Book an appointment with a topic | Must | FR-M4-03, FR-M4-04, FR-M4-05 |
| US-19 | View the day's appointments | Must | FR-M4-06 |
| US-20 | Secretary cancels an office-hour day | Must | FR-M4-07 |
| US-21 | Redirect to a support hour | Should | FR-M4-08 |
| US-22 | Help appointment from a lab reservation | Should | FR-M4-09 |
| US-23 | E-mail notifications | Should | FR-M1-10 |

23 of 23 stories are covered; every FR names exactly one story. Enablers EN-01 … EN-04 are infrastructure and have no FR; their requirements appear as constraints K1, K2 and K7 and in [`nfr.md`](nfr.md).

### 8.3 Endpoint coverage

All 35 operations of OpenAPI v0.2.2 appear in Section 8.1:

| Module | Operations |
|---|---|
| M1 | `registerStudent`, `login`, `getCurrentUser`, `assignUserRole`, `listMyNotifications`, `getUsageReport` |
| M2 | `createReservation`, `getReservation`, `listReservations`, `approveReservation`, `rejectReservation`, `cancelReservation`, `getReservationHistory`, `getMyCalendar` |
| M3 | `listResources`, `getResource`, `createResource`, `updateResource`, `startMaintenance`, `endMaintenance`, `issueHandoverCode`, `checkInReservation`, `returnReservation`, `grantLabApproval`, `listLabApprovals`, `grantTrainingAuthorization`, `listTrainingAuthorizations` |
| M4 | `createOfficeHourSchedule`, `listOfficeHourSchedules`, `listSlots`, `cancelOfficeHourDay`, `bookAppointment`, `listIncomingAppointments`, `listMyAppointments`, `redirectAppointment` |

Sequence diagrams for the main stories are in [`docs/architecture/sequence/`](../architecture/sequence/).

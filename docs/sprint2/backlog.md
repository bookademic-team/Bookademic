# Bookademic – Product Backlog (Sprint 2, refined for Sprint 3)

| Field | Value |
|---|---|
| Document | `docs/sprint2/backlog.md` |
| Owner | Melih (Product Owner & Requirements Engineer) |
| Reviewers | Emir (screens), Sıla (glossary, Issues, process) |
| Issue | [#61](https://github.com/bookademic-team/Bookademic/issues/61) |
| Previous version | [`docs/sprint1/backlog.md`](../sprint1/backlog.md) (kept unchanged as the Sprint 1 record) |
| Requirements | [`srs.md`](srs.md) – every story is linked to its functional requirements (FR) |
| GitHub sync | [`issues.csv`](issues.csv) → GitHub Issues with [`sync_issues.py`](../sprint1/sync_issues.py) |

The backlog contains 10 epics, 21 features, 23 user stories (84 points) and 4 technical enablers (11 points); 95 points in total. Priority is given with MoSCoW, estimates with Fibonacci story points. Story IDs, epics, features, points and owners are unchanged from Sprint 1.

## What changed in Sprint 2

| # | Change | Stories |
|---|---|---|
| B1 | **Sprint column.** Every story and enabler is planned into a sprint: **Sprint 3 = MVP** (all Must stories and EN-04), **Final** = all Should stories. | all |
| B2 | **Issue column.** Each row links to its GitHub Issue; the Issue body is generated from [`issues.csv`](issues.csv), so backlog and Issues stay equal. | all |
| B3 | **FR column.** Each story names the SRS requirements that specify it ([`srs.md` §3](srs.md), traceability in §8). | all |
| B4 | **Start time in the past → 400.** Deferred in Sprint 1 (decision D7, [4 Nov record](../meetings/2026-11-04-sprint1-alignment.md)); now an acceptance criterion. Error code `INVALID_INTERVAL` (existing). OpenAPI v0.2.3 updates the descriptions. | US-06, US-18 (tested in US-07) |
| B5 | **Error codes in acceptance criteria.** Every rejection names the RFC 9457 `code` of the OpenAPI contract, so each criterion maps to one test assertion. | US-01, US-02, US-06, US-08, US-09, US-11, US-14 … US-18, US-21 |
| B6 | **Rules already in the contract, now in the backlog:** rejection and cancellation reasons 5–500 characters (US-08, US-20); appointment owned by the instructor (US-13, US-21); end date of an approval not in the past (US-13); training valid until the end of the semester (US-14); handover code only for approved reservations (US-15); slot length 10–120 minutes (US-17); appointments approved immediately (US-18); counts returned after day cancellation and maintenance (US-16, US-20). | see list |
| B7 | **Concurrency test** added to US-07: two simultaneous requests for the same interval → exactly one succeeds (matches the test plan and ADR-0001). | US-07 |
| B8 | EN-01, EN-02 and EN-03 are **done** (closed in Sprint 1). | EN-01 … EN-03 |

## MVP and sprint plan

**MVP = 15 Must stories (60 points) + EN-04 (3 points), delivered in Sprint 3 (`v0.3.0-sprint3`).** The Should stories (8 stories, 24 points) follow in the final release (`v1.0.0`).

The MVP is the demo journey of the proposal: US-17 (define hours) → US-18 (appointment) → US-13 (lab approval) → US-14 (training and authorization) → US-06 (reservation) → US-08 (research assistant approval). US-20 (secretary cancellation) shows the third actor alongside this journey; US-04 delivers the notifications that US-14 and US-20 require. No Must story depends on a Should story, so the MVP can be finished without any Should work.

**Implementation order in Sprint 3** (from the dependency column):

| Step | Stories / enablers | Why first |
|---|---|---|
| 1 | EN-04, US-01, US-02 | CI and authentication are needed by every other story |
| 2 | US-04, US-11, US-17 | Notifications, catalogue and schedules are the base data of the journey |
| 3 | US-06, US-07 | The M2 conflict engine and `IEligibilityPolicy`; everything else books through it |
| 4 | US-18, US-19, US-12, US-09 | Appointments and catalogue search on top of M2 |
| 5 | US-08, US-13, US-14, US-20 | Approval, lab access and cancellation complete the demo journey |

**Sprint load per person (story + enabler points):**

| Person | Sprint 3 (MVP) | Final (Should) | Total |
|---|:-:|:-:|:-:|
| Melih | 13 | 0 | 13 |
| Sıla | 5 | 9 | 14 |
| Mürsel | 13 | 0 | 13 |
| Mirac | 8 | 5 | 13 |
| Mehmet | 12 | 2 | 14 |
| Emir | 12 | 8 | 20 |
| **Total** | **63** | **24** | **87** |

EN-01 … EN-03 (8 points) were completed in Sprint 1 and are not counted here, so the total is 87 of 95 points. Sıla's Sprint 3 load is lower because the Scrum Master also runs the stand-ups, contribution tracking and the release.

## Epics

| Epic | Name | Module | Priority | Stories | Points | Sprint |
|---|---|---|---|---|---|---|
| E1 | Identity and authorization | M1 | Must | US-01, US-02, US-03 | 8 | 3, Final |
| E2 | Notifications and reporting | M1 | Must | US-04, US-05, US-23 | 9 | 3, Final |
| E3 | Reservation core | M2 | Must | US-06, US-07, US-09, US-10 | 18 | 3, Final |
| E4 | Lab approval flow | M2 | Must | US-08 | 5 | 3 |
| E5 | Resource catalog | M3 | Must | US-11, US-12 | 6 | 3 |
| E6 | Lab access and device training | M3 | Must | US-13, US-14 | 10 | 3 |
| E8 | Office hours and appointments | M4 | Must | US-17, US-18, US-19, US-20 | 15 | 3 |
| E10 | Infrastructure (enablers) | — | Must | EN-01 … EN-04 | 11 | 1, 3 |
| E7 | Check-out/return and maintenance | M3 | Should | US-15, US-16 | 8 | Final |
| E9 | Redirection and help | M4 | Should | US-21, US-22 | 5 | Final |

US-03 in E1, US-05 and US-23 in E2, and US-10 in E3 are Should; an epic's priority follows its main stories.

## Features

| Epic | Feature | Name | Stories / enablers | Points |
|---|---|---|---|---|
| E1 | F1.1 | Registration and sign-in | US-01, US-02 | 6 |
| E1 | F1.2 | Role management | US-03 | 2 |
| E2 | F2.1 | Booking notifications | US-04, US-23 | 6 |
| E2 | F2.2 | Usage reporting | US-05 | 3 |
| E3 | F3.1 | Lab reservation and conflict check | US-06, US-07 | 13 |
| E3 | F3.2 | Cancellation | US-09 | 2 |
| E3 | F3.3 | Unified calendar | US-10 | 3 |
| E4 | F4.1 | Reservation approval | US-08 | 5 |
| E5 | F5.1 | Catalog management | US-11 | 3 |
| E5 | F5.2 | Catalog search | US-12 | 3 |
| E6 | F6.1 | Lab access approval | US-13 | 5 |
| E6 | F6.2 | Device training authorization | US-14 | 5 |
| E7 | F7.1 | Device check-out and return | US-15 | 5 |
| E7 | F7.2 | Maintenance | US-16 | 3 |
| E8 | F8.1 | Office hour scheduling | US-17 | 5 |
| E8 | F8.2 | Appointment booking | US-18, US-19 | 7 |
| E8 | F8.3 | Office hour cancellation | US-20 | 3 |
| E9 | F9.1 | Redirection to support hours | US-21 | 3 |
| E9 | F9.2 | Help from a lab reservation | US-22 | 2 |
| E10 | F10.1 | Repository and API contract | EN-01, EN-02 | 5 |
| E10 | F10.2 | Runtime environment and CI | EN-03, EN-04 | 6 |

21 features in total; every story and enabler belongs to exactly one feature.

## User stories

★ = MVP (Sprint 3). Changes in acceptance criteria since Sprint 1 are listed in [What changed in Sprint 2](#what-changed-in-sprint-2); the new past-start rule is in **bold**.

| ID | Epic | Feature | User story | Acceptance criteria | Points | Priority | Sprint | Issue | FRs (SRS) | Dependencies | Owner |
|---|---|---|---|---|---|---|---|---|---|---|---|
| US-01 | E1 | F1.1 | As a student, I want to register with my school email. | Only emails with the school domain are accepted, otherwise 400 `EMAIL_DOMAIN_NOT_ALLOWED`; registering the same email a second time returns 409 `EMAIL_ALREADY_REGISTERED`; the new account has the role STUDENT. | 3 | Must | ★ 3 | [#5](https://github.com/bookademic-team/Bookademic/issues/5) | FR-M1-01, FR-M1-02 | — | Mehmet |
| US-02 | E1 | F1.1 | As a user, I want to log in and access the screens for my role. | Valid credentials → 200 + JWT; invalid credentials → 401; no token → 401; unauthorized role → 403 `FORBIDDEN`; `GET /users/me` returns the caller's role. | 3 | Must | ★ 3 | [#6](https://github.com/bookademic-team/Bookademic/issues/6) | FR-M1-03, FR-M1-04 | US-01 | Mehmet |
| US-03 | E1 | F1.2 | As an admin, I want to assign roles to staff. | ADMIN only, otherwise 403; undefined role → 400; unknown user → 404; every change is written to the audit log. | 2 | Should | Final | [#7](https://github.com/bookademic-team/Bookademic/issues/7) | FR-M1-05, FR-M1-06 | US-02 | Mehmet |
| US-04 | E2 | F2.1 | As a user, I want to receive an in-app notification when the status of my booking changes. | A notification is created for the affected user on approval, rejection, cancellation, office-hour cancellation, redirection and training authorization; GET /notifications returns only the caller's own notifications, newest first; a failure to create a notification is logged and does not roll back the operation. | 3 | Must | ★ 3 | [#8](https://github.com/bookademic-team/Bookademic/issues/8) | FR-M1-07, FR-M1-08 | US-02 | Mehmet |
| US-05 | E2 | F2.2 | As a department secretary, I want to see the device and office-hour usage report. | Date-range filter (start after end → 400); occupancy rate, reservation count and number of cancellations per device and per office hour; SECRETARY/ADMIN only, otherwise 403. | 3 | Should | Final | [#9](https://github.com/bookademic-team/Bookademic/issues/9) | FR-M1-09 | US-06, US-18 | Sıla |
| US-06 | E3 | F3.1 | As a student, I want to reserve a time slot for a lab device. | End before start → 400 `INVALID_INTERVAL`; **start in the past → 400 `INVALID_INTERVAL`**; device under maintenance → 409 `RESOURCE_IN_MAINTENANCE`; no valid lab approval → 403 `LAB_APPROVAL_REQUIRED`; no training authorization → 403 `TRAINING_REQUIRED`; overlaps a pending, approved or checked-in booking → 409 `SLOT_UNAVAILABLE`, also under concurrent requests; on success 201 + PENDING. | 8 | Must | ★ 3 | [#10](https://github.com/bookademic-team/Bookademic/issues/10) | FR-M2-01 … FR-M2-05 | US-02, US-11 | Mürsel |
| US-07 | E3 | F3.1 | As a QA engineer, I want to verify the conflict engine and access rules with unit tests. | Overlapping, nested and back-to-back ranges (10:00–11:00 and 11:00–12:00 do not conflict); start in the past; all combinations of approval/authorization present or missing; two concurrent requests for the same interval → exactly one succeeds (Testcontainers); M2 coverage at least 50%. | 5 | Must | ★ 3 | [#11](https://github.com/bookademic-team/Bookademic/issues/11) | FR-M2-06 | US-06 | Mirac |
| US-08 | E4 | F4.1 | As a research assistant, I want to approve or reject pending lab requests. | RESEARCH_ASSISTANT/ADMIN only; rejection reason required (5–500 characters); only PENDING requests can be changed, otherwise 409 `INVALID_STATE_TRANSITION`; every transition is written to the status history; the student is notified (US-04). | 5 | Must | ★ 3 | [#12](https://github.com/bookademic-team/Bookademic/issues/12) | FR-M2-07, FR-M2-08, FR-M2-09 | US-06 | Mürsel |
| US-09 | E3 | F3.2 | As a student, I want to cancel my reservation or appointment. | Can be cancelled before the start time, otherwise 409 `CANCELLATION_WINDOW_PASSED`; another user's booking → 403; the status becomes CANCELLED and the slot is freed immediately. | 2 | Must | ★ 3 | [#13](https://github.com/bookademic-team/Bookademic/issues/13) | FR-M2-10 | US-06 | Sıla |
| US-10 | E3 | F3.3 | As a student, I want to see this week's appointments, trainings and reservations in a single calendar. | Chronological list with a type label for the selected range; only the user's own bookings. | 3 | Should | Final | [#14](https://github.com/bookademic-team/Bookademic/issues/14) | FR-M2-11 | US-06, US-18 | Emir |
| US-11 | E5 | F5.1 | As a research assistant, I want to add and edit devices and rooms. | Name, type, category and location required, otherwise 400; serial number unique, duplicate → 409 `DUPLICATE_SERIAL_NUMBER` (also on edit); RESEARCH_ASSISTANT/ADMIN only. | 3 | Must | ★ 3 | [#15](https://github.com/bookademic-team/Bookademic/issues/15) | FR-M3-01, FR-M3-02 | US-02 | Melih |
| US-12 | E5 | F5.2 | As a student, I want to filter the catalog by type, category and availability. | Filters work together (availability = free for the whole requested interval); no results → 200 + empty list; with a 500-record test dataset, p95 response time < 200 ms. | 3 | Must | ★ 3 | [#16](https://github.com/bookademic-team/Bookademic/issues/16) | FR-M3-03 | US-11 | Sıla |
| US-13 | E6 | F6.1 | As an instructor, I want to grant a student lab access approval from an office-hour meeting. | INSTRUCTOR only; the appointment must be an office-hour appointment of the caller, otherwise 403; device category and end date required, end date in the past → 400; the approval is linked to the related appointment; the research assistant sees the approval in the lab panel; an expired approval is invalid. | 5 | Must | ★ 3 | [#17](https://github.com/bookademic-team/Bookademic/issues/17) | FR-M3-04, FR-M3-05, FR-M3-06 | US-18, US-11 | Melih |
| US-14 | E6 | F6.2 | As a research assistant, I want to record a completed device training and authorize the student. | Authorization is created only when an appointment of type TRAINING is completed, otherwise 400 `NOT_A_TRAINING_APPOINTMENT`; authorization is per category and valid until the end of the semester; a second authorization for the same category → 409 `TRAINING_ALREADY_GRANTED`; the instructor who granted the approval receives an in-app notification (US-04). | 5 | Must | ★ 3 | [#18](https://github.com/bookademic-team/Bookademic/issues/18) | FR-M3-07, FR-M3-08 | US-17, US-18, US-04 | Melih |
| US-15 | E7 | F7.1 | As a research assistant, I want to hand over a device with a check-out code and receive its return. | A 6-digit single-use code with an expiry is generated only for an approved reservation, otherwise 409; when verified → CHECKED_IN; a wrong, expired or reused code → 400 `INVALID_HANDOVER_CODE`; on return → COMPLETED. | 5 | Should | Final | [#19](https://github.com/bookademic-team/Bookademic/issues/19) | FR-M3-09, FR-M3-10 | US-08 | Mirac |
| US-16 | E7 | F7.2 | As a research assistant, I want to put a device under maintenance. | Reason required; a device under maintenance cannot be reserved (409 `RESOURCE_IN_MAINTENANCE`); future reservations are cancelled, their owners are notified (US-04) and the number of cancelled reservations is shown; maintenance can be ended. | 3 | Should | Final | [#20](https://github.com/bookademic-team/Bookademic/issues/20) | FR-M3-11 | US-11, US-04 | Sıla |
| US-17 | E8 | F8.1 | As an instructor or research assistant, I want to define weekly office hours, support hours or training hours. | Type (OFFICE_HOUR, SUPPORT, TRAINING), day, time range and slot length (10–120 minutes) are entered; category required for TRAINING; an instructor may define only OFFICE_HOUR, a research assistant only SUPPORT and TRAINING, otherwise 403; the system generates the slots; the same person's hours cannot overlap (409 `SCHEDULE_OVERLAP`). | 5 | Must | ★ 3 | [#21](https://github.com/bookademic-team/Bookademic/issues/21) | FR-M4-01, FR-M4-02 | US-02 | Emir |
| US-18 | E8 | F8.2 | As a student, I want to book an appointment by writing my topic. | Topic note required (10–500 characters), otherwise 400; full slot → 409 `SLOT_UNAVAILABLE`; at most one appointment per day with the same staff member (instructor or research assistant), otherwise 409 `DAILY_APPOINTMENT_LIMIT`; **slot already started → 400 `INVALID_INTERVAL`**; the appointment is stored as a reservation in M2 and is APPROVED immediately. | 5 | Must | ★ 3 | [#22](https://github.com/bookademic-team/Bookademic/issues/22) | FR-M4-03, FR-M4-04, FR-M4-05 | US-17, US-06 | Emir |
| US-19 | E8 | F8.2 | As an instructor, I want to see today's appointments with their topics. | Listed in time order for the selected date; cancelled appointments shown separately; only appointments in the instructor's own hours. | 2 | Must | ★ 3 | [#23](https://github.com/bookademic-team/Bookademic/issues/23) | FR-M4-06 | US-18 | Emir |
| US-20 | E8 | F8.3 | As a department secretary, I want to cancel an office hour for a specific day. | SECRETARY/ADMIN or the owner of the hours only, otherwise 403; reason required (5–500 characters); all appointments of that day become CANCELLED; affected students receive an in-app notification (US-04); the numbers of cancelled appointments and notified students are returned. | 3 | Must | ★ 3 | [#24](https://github.com/bookademic-team/Bookademic/issues/24) | FR-M4-07 | US-18, US-04 | Mirac |
| US-21 | E9 | F9.1 | As an instructor, I want to redirect an appointment to a research assistant's support hours. | Only appointments in the instructor's own hours, otherwise 403; no free slot in the target support hours → 409 `NO_AVAILABLE_SLOT`; the old appointment becomes CANCELLED and a new one is created; the student is notified (US-04). | 3 | Should | Final | [#25](https://github.com/bookademic-team/Bookademic/issues/25) | FR-M4-08 | US-17, US-18, US-04 | Emir |
| US-22 | E9 | F9.2 | As a student, I want to open a help appointment in the assistant's support hours from my lab reservation. | Reservation details are attached to the appointment automatically; only for the student's own APPROVED or CHECKED_IN reservation, otherwise 403. | 2 | Should | Final | [#26](https://github.com/bookademic-team/Bookademic/issues/26) | FR-M4-09 | US-08, US-18 | Emir |
| US-23 | E2 | F2.1 | As a user, I want to receive an email when the status of my booking changes. | Sent for the same events as US-04 through the SMTP service (Mailpit in development); a send failure is logged and does not roll back the operation. | 3 | Should | Final | [#48](https://github.com/bookademic-team/Bookademic/issues/48) | FR-M1-10 | US-04, EN-03 | Sıla |

## Technical enablers

| ID | Feature | Task | Acceptance criterion | Points | Sprint | Issue | Owner |
|---|---|---|---|---|---|---|---|
| EN-01 | F10.1 | GitHub organization, repository, Git-Flow, branch protection, templates | main and develop protected; first PR merged after review | 2 | 1 (done) | [#1](https://github.com/bookademic-team/Bookademic/issues/1) | Mehmet |
| EN-02 | F10.1 | OpenAPI contract v0.2 | Opens without errors in Swagger Editor; every operation is linked to a story via `x-story` (Section 8) | 3 | 1 (done) | [#2](https://github.com/bookademic-team/Bookademic/issues/2) | Mürsel |
| EN-03 | F10.2 | docker-compose skeleton (db + api + web + mailpit) | `docker compose up` starts the db, api, web and mailpit services (10.0 images, as in ADR-0001) | 3 | 1 (done) | [#3](https://github.com/bookademic-team/Bookademic/issues/3) | Mehmet |
| EN-04 | F10.2 | GitHub Actions CI (build + test + coverage) | Runs on every PR; fails if coverage is below 50% | 3 | 3 | [#4](https://github.com/bookademic-team/Bookademic/issues/4) | Mehmet |

## Summary

| | Stories | Points | Sprint |
|---|---|---|---|
| Must | 15 | 60 | 3 (MVP) |
| Should | 8 | 24 | Final |
| Enablers | 4 | 11 | 1 (3 done), 3 (EN-04) |
| **Total** | **27** | **95** | |

## Keeping GitHub Issues equal to this backlog

[`issues.csv`](issues.csv) holds one row per story and enabler with the Issue title, body (acceptance criteria, sprint, FRs, owner, Definition of Done), labels and owner. After this file is merged, the Product Owner runs the Sprint 1 sync script from this folder:

```bash
cd docs/sprint2
python3 ../sprint1/sync_issues.py bookademic-team/Bookademic           # dry run
python3 ../sprint1/sync_issues.py bookademic-team/Bookademic --apply   # update the Issues
```

The script matches Issues by the ID prefix of their title (`US-18:`), updates title, body, labels and assignee, and creates missing Issues. Labels follow [`git-flow.md` §5](../sprint1/git-flow.md) and are unchanged; the sprint is written in the Issue body.

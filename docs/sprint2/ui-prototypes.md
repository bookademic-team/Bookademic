# Bookademic — UI Prototypes (Figma)

| Field | Value |
|---|---|
| File | `docs/sprint2/ui-prototypes.md` |
| Sprint | 2 |
| Issue | Refs #67 |
| Author | Emir (M4 – Office Hours) |
| Figma file | _link will be added when the file is ready_ |
| Related docs | `docs/ui-screens.md` (screen list and flows), `docs/api/openapi.yaml` v0.2.3 |
| Language rule | All UI text is in English. |

## 1. Purpose

This document links the Figma prototypes to the screen list in `docs/ui-screens.md`. Frontend work in Sprint 2 follows the Figma file, this document and the OpenAPI contract. If a screen differs between Figma and `ui-screens.md`, `ui-screens.md` is updated or the difference is raised in the PR.

## 2. Screen to Figma frame map

Frame names in Figma use the same codes as `ui-screens.md`.

| Code | Screen | Role | Story | Priority | Figma frame | Status |
|---|---|---|---|---|---|---|
| SCR-01 | Sign In & Register | All users | US-01, US-02 | Must | `SCR-01 Sign In & Register` | Not started |
| SCR-02 | Calendar & My Appointments | Student | US-10, US-09 | Calendar: Should · Cancel: Must | `SCR-02 Calendar & My Appointments` | Not started |
| SCR-03 | Office Hours Management | Instructor, Research Assistant | US-17 | Must | `SCR-03 Office Hours Management` | Not started |
| SCR-04 | Book Appointment | Student | US-18 | Must | `SCR-04 Book Appointment` | Not started |
| SCR-05 | Daily Appointments & Lab Approval | Instructor | US-19, US-13 | Must | `SCR-05 Daily Appointments & Lab Approval` | Not started |
| SCR-06 | Device Training & Authorization | Student, Research Assistant | US-18, US-14 | Must | `SCR-06 Device Training & Authorization` | Not started |
| SCR-07 | Lab Catalog & Reservation | Student | US-12, US-06 | Must | `SCR-07 Lab Catalog & Reservation` | Not started |
| SCR-08 | Lab Management & Check-out/Return | Research Assistant, Admin | US-08, US-13, US-15, US-16 | US-08, US-13: Must · others: Should | `SCR-08 Lab Management & Check-out/Return` | Not started |
| SCR-09 | Secretary & Admin Management | Secretary, Admin | US-20, US-05, US-03 | US-20: Must · others: Should | `SCR-09 Secretary & Admin Management` | Not started |
| SCR-10 | Redirect & Help Request | Instructor, Student | US-21, US-22 | Should | `SCR-10 Redirect & Help Request` | Not started |
| SCR-11 | Device/Room Catalog Management | Research Assistant, Admin | US-11 | Must | `SCR-11 Device/Room Catalog Management` | Not started |
| CMP-01 | Notification Center (header component) | All signed-in roles | US-04 | Must (in-app) | `CMP-01 Notification Center` | Not started |

Status values: Not started · Draft · Ready for review · Approved by PO.

## 3. Prototype scope

- **Must screens first:** SCR-01, SCR-03, SCR-04, SCR-05, SCR-06, SCR-07, SCR-08 (approval part), SCR-11 and CMP-01.
- **Should screens:** SCR-02 calendar, SCR-08 check-out/return and maintenance, SCR-09 report and role assignment, SCR-10.
- **Clickable flow:** the MVP journey from `docs/ui-screens.md` §5: SCR-01 → SCR-04 → SCR-05 → SCR-06 → SCR-07 → SCR-08.

## 4. States every list screen must show

Each list screen has a frame (or variant) for all four states: loading, empty, error, populated. Empty-state texts are in `docs/ui-screens.md` §6.

## 5. Error messages

Messages in Figma follow `docs/ui-screens.md` §8 (server `code` → message). Key states to design:

| `code` | Screen | Message |
|---|---|---|
| `SLOT_UNAVAILABLE` | SCR-04, SCR-06, SCR-07 | "The selected time is already taken. Please choose another time." |
| `DAILY_APPOINTMENT_LIMIT` | SCR-04 | "You can book only one appointment per day with the same staff member (instructor or research assistant)." |
| `LAB_APPROVAL_REQUIRED` | SCR-07 | "You need lab access approval from an instructor for this device." + link to SCR-04 |
| `TRAINING_REQUIRED` | SCR-07 | "You need to complete training for this device category." + link to SCR-06 |

## 6. Review and approval

| Step | Owner | Status |
|---|---|---|
| Figma file shared with the team | Emir | In progress |
| Figma PR opened | Emir | In progress |
| Product Owner approval | Melih (PO) | Waiting |

## 7. Out of scope

Pixel-level visual design, mobile app, live queue, messaging (see `docs/sprint1/vision.md` §3.2).

# Bookademic — UI Prototypes (Figma)

| Field | Value |
|---|---|
| File | `docs/sprint2/ui-prototypes.md` |
| Sprint | 2 |
| Issue | Refs #67 |
| Author | Emir (M4 – Office Hours) |
| Figma file | [Bookademic UI Prototypes](https://www.figma.com/design/ehxDJIgA44wXwVTPlbrzy8/Bookademic-UI-Prototypes?node-id=0-1) (view access for anyone with the link) |
| Related docs | `docs/ui-screens.md` (screen list and flows), `docs/api/openapi.yaml` v0.2.3 |
| Language rule | All UI text is in English. |

## 1. Purpose

This document links the Figma prototypes to the screen list in `docs/ui-screens.md`. Frontend work in Sprint 2 follows the Figma file, this document and the OpenAPI contract. If a screen differs between Figma and `ui-screens.md`, `ui-screens.md` is updated or the difference is raised in the PR.

## 2. Figma file structure

| Figma page | Content |
|---|---|
| `Main journey` | The MVP screens with error variants, connected as a clickable prototype (flow `MVP journey`). |
| `Other screens and states` | The remaining screens, empty states and error states (static frames). |

## 3. Screen to Figma frame map

Frame names in Figma use the same codes as `ui-screens.md`.

| Code | Screen | Role | Story | Priority | Figma frame | Figma page | Status |
|---|---|---|---|---|---|---|---|
| SCR-01 | Sign In & Register | All users | US-01, US-02 | Must | `SCR-01 Sign In & Register` | Main journey | Ready for review |
| SCR-02 | Calendar & My Appointments | Student | US-10, US-09 | Calendar: Should · Cancel: Must | `SCR-02 Calendar & My Appointments` | Other screens and states | Ready for review |
| SCR-03 | Office Hours Management | Instructor, Research Assistant | US-17 | Must | `SCR-03 Office Hours Management` | Other screens and states | Ready for review |
| SCR-04 | Book Appointment | Student | US-18 | Must | `SCR-04 Book Appointment` | Main journey | Ready for review |
| SCR-05 | Daily Appointments & Lab Approval | Instructor | US-19, US-13 | Must | `SCR-05 Daily Appointments & Lab Approval` | Main journey | Ready for review |
| SCR-06 | Device Training & Authorization | Student, Research Assistant | US-18, US-14 | Must | `SCR-06 Device Training & Authorization` | Main journey | Ready for review |
| SCR-07 | Lab Catalog & Reservation | Student | US-12, US-06 | Must | `SCR-07 Lab Catalog & Reservation` | Main journey | Ready for review |
| SCR-08 | Lab Management & Check-out/Return | Research Assistant, Admin | US-08, US-13, US-15, US-16 | US-08, US-13: Must · others: Should | `SCR-08 Lab Management & Check-out/Return` | Main journey | Ready for review |
| SCR-09 | Secretary & Admin Management | Secretary, Admin | US-20, US-05, US-03 | US-20: Must · others: Should | `SCR-09 Secretary & Admin Management` | Other screens and states | Ready for review |
| SCR-10 | Redirect & Help Request | Instructor, Student | US-21, US-22 | Should | `SCR-10 Redirect & Help Request` | Other screens and states | Ready for review |
| SCR-11 | Device/Room Catalog Management | Research Assistant, Admin | US-11 | Must | `SCR-11 Device/Room Catalog Management` | Other screens and states | Ready for review |
| CMP-01 | Notification Center (header component) | All signed-in roles | US-04 | Must (in-app) | `CMP-01 Notification Center` | Other screens and states | Ready for review |

Status values: Not started · Draft · Ready for review · Approved by PO.

Frames on `Other screens and states` are static images. They are not connected in the prototype.

## 4. Prototype scope

- **Must screens first:** SCR-01, SCR-03, SCR-04, SCR-05, SCR-06, SCR-07, SCR-08 (approval part), SCR-11 and CMP-01.
- **Should screens:** SCR-02 calendar, SCR-08 check-out/return and maintenance, SCR-09 report and role assignment, SCR-10.
- **Clickable flow (`MVP journey`):** SCR-01 → SCR-04 → SCR-05 → SCR-06 → SCR-07 → SCR-08. Navigation is on the primary buttons of each screen. Text fields are not editable in the prototype.

## 5. States covered in Figma

Error and empty states are separate frames (frame name + ` / ` + state name).

| Screen | State frames |
|---|---|
| SCR-01 | Error (invalid sign-in) |
| SCR-02 | Empty list |
| SCR-03 | Error 409 |
| SCR-04 | Error 409 (`SLOT_UNAVAILABLE`) |
| SCR-07 | Error variant (missing approval or training) |
| SCR-11 | Error 409 |
| ERR-403 | Forbidden page |

List screens that have no separate loading frame use the populated frame as the reference. Empty-state texts are in `docs/ui-screens.md` §6.

##

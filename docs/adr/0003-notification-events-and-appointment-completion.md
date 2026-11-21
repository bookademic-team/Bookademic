# ADR 0003 – Notification Events and Appointment Completion

| Field | Value |
|---|---|
| Status | Accepted (21 Nov 2026, approved in the pull-request review) |
| Date | 2026-11-20 |
| Decision owner (A) | Mürsel – Lead Software Architect |
| Consulted (C) | Mehmet (M1 Identity owner), Sıla (US-23 owner), Emir (M4 Office Hours) |
| Informed (I) | Mirac (QA), Melih (PO) |
| Related work | US-04, US-16, US-20, US-21, US-23, [ADR-0001](0001-tech-stack.md), [C4 components](../architecture/c4.md#3-level-3--components-of-the-api), [UML state machine](../architecture/uml.md) |

## Context

The system sends six kinds of notifications (`NotificationType` in OpenAPI v0.2.2): `RESERVATION_APPROVED`, `RESERVATION_REJECTED`, `RESERVATION_CANCELLED`, `OFFICE_HOUR_CANCELLED`, `APPOINTMENT_REDIRECTED`, `TRAINING_GRANTED`. Each one is shown in the app (US-04, `GET /notifications`) and sent as e-mail through the **SMTP service** (US-23; Mailpit in development).

The things that cause a notification happen in different modules: M2 Booking Core changes reservation status, M3 Lab grants training and starts maintenance, M4 Office Hours cancels days and redirects appointments. Notifications are owned by M1 Identity. ADR-0001 does not allow modules to reference each other's internal projects. The C4 component diagram already shows "M2 → M1: status-change events", but the mechanism was not defined.

A second, related gap is in the [UML state machine](../architecture/uml.md): `APPROVED → COMPLETED` is allowed, but for `OFFICE_HOUR` and `SUPPORT` appointments **no endpoint completes them**. Lab reservations are completed by `POST /reservations/{id}/return` and `TRAINING` appointments by `POST /training-authorizations` (SRS FR-M3-07), but the other appointments would stay `APPROVED` forever. This makes "my appointments" lists and usage reports wrong.

## Decision

### 1. In-process domain events

Modules publish **events** through a small interface; other modules subscribe to them. Everything runs inside the single API process (modular monolith).

```csharp
// Bookademic.SharedKernel (referenced by every module)
public interface IEventPublisher
{
    Task PublishAsync<TEvent>(TEvent e, CancellationToken ct = default);
}

public interface IEventHandler<in TEvent>
{
    Task HandleAsync(TEvent e, CancellationToken ct);
}
```

Event types live in each module's **contracts** project, so a subscriber depends only on contracts, never on internals:

| Event | Published by | Contracts project | Fields |
|---|---|---|---|
| `ReservationStatusChanged` | M2 Booking Core (every transition) | `Bookademic.Booking.Contracts` | `ReservationId`, `Kind`, `OwnerId`, `From`, `To`, `ChangedBy`, `Cause`, `Reason`, `OccurredAt` |
| `OfficeHourDayCancelled` | M4 Office Hours (US-20) | `Bookademic.OfficeHours.Contracts` | `ScheduleId`, `HostId`, `Date`, `Reason`, affected `AppointmentIds` and `StudentIds` |
| `AppointmentRedirected` | M4 Office Hours (US-21) | `Bookademic.OfficeHours.Contracts` | `OldAppointmentId`, `NewAppointmentId`, `StudentId`, `NewHostId` |
| `TrainingGranted` | M3 Lab (US-14) | `Bookademic.Lab.Contracts` | `TrainingAuthorizationId`, `StudentId`, `DeviceCategory`, `GrantedBy`, `ApprovingInstructorIds` (M3 reads them from its own `lab_approvals`, so M1 does not query M3 tables) |

`Cause` is an enum `USER`, `MAINTENANCE`, `DAY_CANCELLED`, `REDIRECTED`, `AUTO_COMPLETED`. M3 and M4 pass it to `IBookingEngine` when they cancel or complete a reservation, so M2 can publish it without knowing why the module made the change.

**Events are published after the database transaction commits.** A handler therefore never sees a change that is later rolled back. The dispatcher is a simple class that resolves all `IEventHandler<T>` from dependency injection and calls them in turn. An exception in a handler is logged and does not fail the user's request: the reservation change has already been saved.

### 2. In-app notifications (M1)

M1 has one handler per event. It writes a row to `identity.notifications` with the text in English. The mapping rules ensure that **one action gives one notification**:

| Event and condition | NotificationType | Recipient |
|---|---|---|
| `ReservationStatusChanged`, `To = APPROVED`, `Kind = LAB` | `RESERVATION_APPROVED` | Owner |
| `ReservationStatusChanged`, `To = REJECTED` | `RESERVATION_REJECTED` | Owner (with the reason) |
| `ReservationStatusChanged`, `To = CANCELLED`, `Cause = USER` or `MAINTENANCE` | `RESERVATION_CANCELLED` | The party who did not cancel: the owner if staff cancelled; the host if a student cancelled an appointment |
| `OfficeHourDayCancelled` | `OFFICE_HOUR_CANCELLED` | Every affected student |
| `AppointmentRedirected` | `APPOINTMENT_REDIRECTED` | Student |
| `TrainingGranted` | `TRAINING_GRANTED` | The instructor(s) who granted the student an active lab approval for the same category (SRS FR-M3-08); nobody if there is none |
| `ReservationStatusChanged` with `Cause = DAY_CANCELLED` or `REDIRECTED` | none (the specific event above covers it) | – |
| Creation (`From = null`), `CHECKED_IN`, `COMPLETED` | none | – |

A new appointment is approved automatically, so there is no notification for it. The host sees it in `GET /appointments/incoming` (US-19).

### 3. E-mail (US-23)

- After writing the notification row, M1 puts an `EmailMessage` into a bounded in-memory queue (`System.Threading.Channels`).
- A `BackgroundService` in M1 takes messages from the queue and sends them with MailKit to the SMTP service (`mailpit:1025` in docker compose; host and port come from configuration).
- A failed send is tried 3 times (after 5 s, 30 s, 2 min) and is then logged. **The in-app notification is the source of truth.** A lost e-mail does not lose the information.
- The user's request never waits for SMTP. This keeps reservation endpoints fast, and the system keeps working when Mailpit is down.

### 4. Automatic completion of appointments

M4 runs a `BackgroundService` called `AppointmentCompletionJob` every 5 minutes:

1. It finds `OFFICE_HOUR` and `SUPPORT` appointments whose reservation status is `APPROVED` and whose `endsAt` is in the past.
2. For each one it calls `IBookingEngine.CompleteAsync(id, changedBy: hostId, cause: AUTO_COMPLETED, reason: "Completed automatically after the end time")`.
3. M2 writes the `status_changes` row and publishes `ReservationStatusChanged`, as for any other transition. No notification is sent.

The `APPROVED → COMPLETED` transition is already allowed by the contract, so **no new endpoint and no contract change** are needed. `changedBy` is the host, because the appointment took place in the host's time. This keeps `changed_by` a real user id and avoids a special "system" user that is not a `Role`.

`TRAINING` appointments are **not** completed by the job. The research assistant completes them with `POST /training-authorizations`, which sets the appointment to `COMPLETED` and creates the authorisation in one transaction (SRS FR-M3-07). If the job completed them first, that request would fail, and an appointment marked `COMPLETED` by time alone would look like a finished training even though no authorisation was given.

Lab reservations are **not** completed automatically either: they are completed at return (`POST /reservations/{id}/return`, US-15), because the device must physically come back. What happens to an `APPROVED` lab reservation that is never checked in (no-show) is decided in Sprint 3.

### 5. Tests

| Test | Type |
|---|---|
| Approving a lab reservation creates exactly one `RESERVATION_APPROVED` notification for the owner | Integration (Testcontainers) |
| Day cancellation with 3 appointments creates 3 `OFFICE_HOUR_CANCELLED` notifications and no `RESERVATION_CANCELLED` | Integration |
| A rolled-back transaction publishes no event | Integration |
| SMTP down: the request still returns 200/201 and the notification row exists | Integration (Mailpit stopped) |
| The e-mail arrives in Mailpit (Mailpit API `GET /api/v1/messages`) | Integration / E2E |
| The job completes an `APPROVED` appointment after `endsAt`, leaves future ones, `TRAINING` appointments and lab reservations unchanged | Unit (fake clock with `TimeProvider`) |
| M2 has no reference to M1, M3 or M4 internals | ArchUnitNET |

## Consequences

**Positive**

- M2, M3 and M4 do not know that M1 or e-mail exist. They only publish events. A new notification channel later (for example push) is only a new handler.
- One event stream feeds both US-04 and US-23, as written in the story mapping.
- Appointment lists, history and usage reports show the real final status.
- No new container, no new library: only the .NET base library and MailKit, which is already chosen.

**Negative and accepted**

- If the API process stops between the commit and the handler, that notification is lost. For a course project this risk is small, and the reservation itself is safe. A transactional outbox is the fix if it becomes a problem.
- Handlers run in the same request, after commit, so they add a few milliseconds to it. E-mail is sent in the background, so the slow part is not in the request.
- The in-memory e-mail queue is lost on restart. Messages that were not sent stay visible as in-app notifications.
- An appointment is shown as `COMPLETED` up to 5 minutes after it ends.

## Alternatives considered

**Direct call from M2 to an M1 service.** Rejected. M2 Booking Core would depend on M1 and would have to know which status changes need which notification. That breaks the rule that the core does not know module-specific details.

**MediatR.** Rejected. The interfaces needed here are about 30 lines of code. MediatR moved to a commercial licence model in 2025, while the course requires MIT-compatible dependencies.

**Transactional outbox (event rows written in the same transaction, sent by a worker).** Postponed. It guarantees that no event is lost, but it needs an extra table, a worker and handling of duplicate messages. It is the upgrade path if lost notifications become a real problem.

**Message broker (RabbitMQ, Kafka).** Rejected. It adds a container and operational work for a single-process modular monolith.

**Database triggers that write notifications.** Rejected. Business rules would be hidden in SQL, M2's tables would write into M1's schema, and the rules would be hard to unit test.

**Endpoint for completing appointments (`POST /appointments/{id}/complete`).** Rejected. Hosts would have to remember to click it after every appointment, and it would need a contract change. Automatic completion gives the same result without any user action.

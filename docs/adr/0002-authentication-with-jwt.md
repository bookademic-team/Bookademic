# ADR 0002 – Authentication with JWT

| Field | Value |
|---|---|
| Status | Accepted (21 Nov 2026, approved in the pull-request review) |
| Date | 2026-11-20 |
| Decision owner (A) | Mürsel – Lead Software Architect |
| Consulted (C) | Mehmet (M1 Identity owner, DevOps), Mirac (QA) |
| Informed (I) | Emir (Implementation), Melih (PO), Sıla (SM) |
| Related work | US-01, US-02, US-03, [ADR-0001](0001-tech-stack.md), [OpenAPI v0.2.3](../api/openapi.yaml) (`bearerAuth`, `POST /auth/login`, `TokenResponse`), [C4 containers](../architecture/c4.md#2-level-2--containers) |

## Context

ADR-0001 chose "ASP.NET Core JWT Bearer + role-based policies" but did not fix the details. These details affect every module and the frontend, so they are decided here once:

- The contract already defines the shape: every operation except `POST /auth/register` and `POST /auth/login` requires `bearerAuth`. Login returns a `TokenResponse` (`accessToken`, `tokenType: Bearer`, `expiresIn: 3600`, `user`). There is **no refresh or logout endpoint** in v0.2.3.
- The **Web App** (port 5173) and the **API** (port 8080) run on different origins. Cookie-based sessions would need CORS with credentials and CSRF protection.
- There are 5 roles: `STUDENT`, `INSTRUCTOR`, `RESEARCH_ASSISTANT`, `SECRETARY`, `ADMIN`. Many rules also depend on ownership: a student may cancel only their own reservation, and an instructor may redirect only their own appointments.
- Errors use RFC 9457 problem details: `401 UNAUTHORIZED` (missing, invalid or expired token, or wrong credentials) and `403 FORBIDDEN` (the role or ownership does not allow the action).
- KVKK: only the data the system needs is stored (see [database.md](../architecture/database.md#5-data-protection-kvkk)).

## Decision

### 1. Token

| Aspect | Decision |
|---|---|
| Type | Stateless JWT **access token**, sent as `Authorization: Bearer <token>` |
| Signing | HS256 with a symmetric key of at least 32 bytes. The key comes from the environment variable `Jwt__SigningKey` (in `.env` for docker compose, never committed). Adding it to `.env.example` and `docker-compose.yml` is a follow-up (see below) |
| Issuer / audience | `iss = bookademic-api`, `aud = bookademic-web`, both validated |
| Lifetime | 60 minutes (`expiresIn: 3600`). Clock skew is 1 minute instead of the default 5 |
| Claims | `sub` (user id), `email`, `name`, `role`, `jti`, `iat`, `exp` |
| Refresh | None in the current scope. When the token expires, the API returns 401 and the Web App sends the user to the login screen |
| Logout | Client side only: the Web App deletes the token. There is no server-side revocation |

Claims are kept short and contain no other personal data. The claim names are used as they are: `MapInboundClaims = false`, `RoleClaimType = "role"`, `NameClaimType = "name"`.

### 2. Login and passwords

- Passwords are hashed with ASP.NET Core `PasswordHasher<User>` (PBKDF2, Identity v3 format). The plain password is never logged or stored.
- `POST /auth/login` returns the **same 401** for an unknown e-mail and for a wrong password, so the response does not reveal which accounts exist.
- `POST /auth/register` creates only `STUDENT` accounts with a school e-mail domain (US-01). Staff roles are given only by an admin through `PUT /admin/users/{userId}/role` (US-03), and every change is written to `identity.audit_log`.
- Development and demo accounts for every role are created by a seed script with synthetic data.

### 3. Authorization: two layers

| Layer | Where | Example | Error |
|---|---|---|---|
| Role | Endpoint attribute or policy: `[Authorize(Roles = "...")]`, named policies such as `StaffOnly` (`INSTRUCTOR`, `RESEARCH_ASSISTANT`), `CanCancelOfficeHours` (`SECRETARY`, `ADMIN`, host) | `PUT /admin/users/{userId}/role` → `ADMIN` only | 403 `FORBIDDEN` |
| Ownership | Inside the module's handler, comparing `sub` with the record | Cancel: `reservation.ownerId == sub`; redirect: `appointment.hostId == sub` | 403 `FORBIDDEN` |

The roles allowed for each operation are the ones written in the operation summaries of the OpenAPI contract. The API is the only authority: the Web App reads the role from `GET /users/me` and hides screens and menu items with React Router guards, but hiding is only for convenience.

A role claim stays in the token until the token expires. **A role change therefore takes effect at the user's next login, at most 60 minutes later.** This is accepted (see consequences).

### 4. Web App

- The token is kept in memory and in `sessionStorage` (it is cleared when the tab is closed, so it does not stay on shared lab computers). It is not stored in `localStorage` or in cookies.
- `src/api/client.js` (the single API layer from ADR-0001) adds the `Authorization` header to every call. On any 401 it deletes the token and redirects to `/login`.
- React renders text with escaping and the code does not use `dangerouslySetInnerHTML`, which reduces the XSS risk of keeping the token in the browser.

### 5. API configuration (sketch)

```csharp
builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.MapInboundClaims = false;
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidIssuer = "bookademic-api",
            ValidAudience = "bookademic-web",
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:SigningKey"]!)),
            ClockSkew = TimeSpan.FromMinutes(1),
            RoleClaimType = "role",
            NameClaimType = "name"
        };
        // OnChallenge / OnForbidden write RFC 9457 problem+json
        // with code UNAUTHORIZED / FORBIDDEN, as in the contract.
    });

builder.Services.AddAuthorizationBuilder()
    .AddPolicy("StaffOnly", p => p.RequireRole("INSTRUCTOR", "RESEARCH_ASSISTANT"))
    .SetFallbackPolicy(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

The **fallback policy** protects every endpoint by default; only `register` and `login` are marked `[AllowAnonymous]`. A new endpoint that someone forgets to protect is therefore protected anyway.

The API starts only if the signing key exists and is at least 32 bytes long. Without it, startup fails with a clear message instead of signing tokens with a weak key.

### 6. Tests (for `docs/test-plan.md`)

| Case | Expected |
|---|---|
| No token, malformed token, expired token, wrong signature, wrong audience | 401 `UNAUTHORIZED` |
| Valid token, wrong role (e.g. `STUDENT` calls `PUT /admin/users/{id}/role`) | 403 `FORBIDDEN` |
| Valid token, right role, someone else's record (cancel another student's reservation) | 403 `FORBIDDEN` |
| Unknown e-mail vs. wrong password at login | Same 401 body |
| Every endpoint except `register` and `login` | 401 without a token (one test enumerates the endpoints) |

## Consequences

**Positive**

- Matches the contract exactly (`bearerAuth`, `TokenResponse`); no API change is needed.
- Stateless: the API does not keep sessions. There are no cookies, so there is no CSRF and the CORS setup stays simple.
- Uses only built-in ASP.NET Core packages (MIT licence).
- Prism mock and Swagger UI work with the same header.

**Negative and accepted**

- A role change, or disabling a user, takes effect only after the token expires (at most 60 minutes). The impact is small in a department system, and admins are told about it in the user guide.
- Logout does not invalidate a stolen token before it expires. The short lifetime limits this.
- The user must log in again every hour. This is acceptable for short booking sessions.
- The token in `sessionStorage` can be read by script on the page if an XSS bug exists. Escaping by default and the short lifetime limit this.

**Follow-ups**

- `Jwt__SigningKey` is not yet in the repository. It is added as a placeholder to `.env.example` and passed to the `api` service in `docker-compose.yml`, and listed in the environment-variable table of the deployment document (#71). Owner: Mehmet. Until then the API refuses to start without the key (section 5), which is intended.
- Rate limiting on `POST /auth/login` (built-in ASP.NET Core rate limiter, e.g. 5 attempts per minute per IP) needs a `429` response in the contract. It is planned for a later contract version (owner Mürsel); v0.2.3 changes descriptions only.
- In production the API runs behind TLS (HTTPS). In local docker compose, plain HTTP on `localhost` is used.

## Alternatives considered

**Cookie-based session authentication.** Rejected. Web App and API are on different origins, so it needs CORS with credentials and anti-CSRF tokens. It also does not match the `bearerAuth` scheme in the contract.

**ASP.NET Core Identity with its built-in API endpoints (`MapIdentityApi`).** Rejected. Its endpoints and response shapes (`/register`, `/login`, opaque tokens) differ from the agreed contract, and it creates many tables (`AspNetUsers`, `AspNetRoles`, …) that the project does not need. Only `PasswordHasher` is reused.

**External identity provider (Keycloak, university SSO).** Rejected. Keycloak adds another container and its own configuration to learn. The team has no access to the university SSO.

**Access token + refresh token.** Postponed. It needs new endpoints (`/auth/refresh`, `/auth/logout`) and token storage in the database. It can be added later without breaking existing clients if one-hour sessions turn out to be a problem.

**RS256 (asymmetric keys).** Not needed now. Only the API signs and verifies tokens, so one symmetric key is enough. If modules are ever split into separate services, RS256 with a published public key is the upgrade path.

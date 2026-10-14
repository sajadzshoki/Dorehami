# API Contracts — Dorehami

Conventions only — **no endpoints are implemented in Phase 0**. Nitro server routes under `/api/v1/…` (Provisional prefix; SSR pages call the same application services, not HTTP self-calls). This document constrains Phase 1+ implementation.

## 1. General conventions

- JSON over HTTPS; UTF-8; request/response field names `camelCase` in JSON, `snake_case` in DB.
- All timestamps ISO 8601 UTC in API payloads (`startsAt`), plus occurrence payloads include the event `timezone` so clients render local/Jalali correctly. Clients never guess timezone from locale alone.
- IDs are opaque strings (UUID). Slugs used only in public page URLs, not as API identifiers.
- Locale: content endpoints accept the route locale (`/fa/…`, `/en/…` pages; `Accept-Language`/explicit `locale` for API — Phase 1 settles one mechanism and documents it here).

## 2. Response envelope

Success: `{ "data": …, "meta": { … } }`. Error: §4. Lists include `meta.pagination`.

## 3. Pagination, filtering, sorting

- Cursor pagination for feeds (posts, messages, notifications): `?cursor=&limit=` (limit default 20, max 50, Provisional). Offset pagination acceptable for admin tables only.
- Discovery filters: `category`, `city`, `format`, `from`, `to`, `q`, `communityId`, `organizerId` (all validated; unknown params → `400`).
- Sort orders are whitelisted per endpoint; default event discovery sort = upcoming occurrences ascending (then relevance when `q` present).

## 4. Errors

Stable machine-readable codes; human messages localized, never leaking internals or stack traces.

```json
{ "error": { "code": "EVENT_FULL", "message": "This occurrence is fully booked.", "details": null, "requestId": "…" } }
```

| HTTP | Codes (initial set) |
|---|---|
| 400 | `VALIDATION_FAILED` (with per-field `details`), `INVALID_FILTER` |
| 401 | `UNAUTHENTICATED`, `OTP_EXPIRED`, `OTP_INVALID`, `OTP_LOCKED` |
| 403 | `FORBIDDEN`, `NOT_A_PARTICIPANT`, `CAPABILITY_REQUIRED`, `ACCOUNT_SUSPENDED` |
| 404 | `NOT_FOUND` (also used to avoid resource enumeration where required) |
| 403 (cont.) | `MESSAGE_BLOCKED` (generic delivery failure shown to a blocked sender — must not confirm block status, DOMAIN-MODEL §12), `ORGANIZER_SUSPENDED`, `ACCOUNT_DELETION_PENDING` |
| 409 | `ALREADY_REGISTERED`, `EVENT_FULL`, `INVALID_STATE_TRANSITION`, `CAPACITY_BELOW_REGISTRATIONS`, `REVISION_CONFLICT` (stale `revision` on occurrence/event edit — client must reload), `REGISTRATION_CLOSED` |
| 429 | `RATE_LIMITED` (with `Retry-After`) |
| 502/503 | `PROVIDER_UNAVAILABLE` (e.g. SMS send failed — never fake-success) |

`requestId` on every response; logged server-side (redacted) for correlation.

## 5. Validation & authorization principles

- Every input validated at the route boundary with a schema (unknown/extra fields rejected or stripped consistently — Phase 1 picks one); domain services re-check invariants (capacity, state transitions) inside transactions.
- **Every** handler: (1) authenticate if required, (2) load resource, (3) authorize against the specific resource (owner/manager/participant/admin), (4) only then mutate. BOLA/IDOR tests are mandatory per endpoint (THREAT-MODEL T-04).
- Mutations requiring confirmation semantics (series cancellation, account deletion) require explicit payload flags and re-authentication where specified.
- Idempotency: registration creation must be safe under client retry (duplicate → `409 ALREADY_REGISTERED` with the existing registration in `details`, not a second row). OTP verify is single-use by design.

## 6. Versioning & compatibility

`/api/v1` from day one; additive changes preferred; breaking changes require a new version and a note in this file. Public page URL policy is in [SEO-AND-PERFORMANCE](SEO-AND-PERFORMANCE.md) §2.

## 7. Representative resource map (names only, Phase 4+ defines payloads)

`/api/v1/auth/otp/request|verify`, `/auth/logout`, `/me`, `/me/profile`, `/me/interests`, `/me/privacy`, `/me/registrations`, `/events`, `/events/:id`, `/events/:id/occurrences`, `/occurrences/:id/registrations`, `/communities`, `/communities/:id/memberships`, `/posts`, `/comments`, `/reactions`, `/conversations`, `/messages`, `/notifications`, `/reports`, `/media/uploads`, `/admin/…`, `/organizer/…`. ICS: `/events/:id/occurrences/:occurrenceId.ics` (authenticated or signed-token policy decided in Phase 6 — D-14).

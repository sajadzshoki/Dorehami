# MVP Acceptance Criteria — Dorehami

Measurable criteria verified in Phase 8 (many land with their feature phase). A criterion passes only with an automated test or a documented manual check executed and recorded.

## Auth & accounts (AC-AUTH)
1. OTP login completes end-to-end with the real provider in a production-like environment; mock provider is rejected at boot in production mode (T-22).
2. Expired, reused, and brute-forced OTPs are rejected with stable error codes; lockout after 5 attempts; resend cooldown enforced (tests).
3. Logout revokes the session server-side (replayed token → 401); login rotates the session token.
4. Optional password set/change works with hashing per baseline; password login is rate-limited.
5. Onboarding (interests + city + privacy defaults) is completable in fa (RTL, Jalali irrelevant here) and en, keyboard-only.

## Discovery & events (AC-EVENT)
6. Public discovery without login: search + filters (category, city, time, format) return only published content; Persian/Arabic query variants match (test corpus).
7. Event pages show localized date/time, format/location, capacity state, organizer, community link; cancelled occurrences are clearly marked and unregistrable.
8. Registration: 50 parallel attempts at capacity 10 → exactly 10 confirmed; duplicate attempts return `ALREADY_REGISTERED` without a second row; retries are safe.
9. Attendee cancellation frees a seat immediately (subsequent registration succeeds); organizer capacity reduction below confirmed count is rejected.
10. Recurring series: occurrences materialize per rule in the event timezone; cancelling one occurrence leaves others registrable; series cancellation cancels/notifies future occurrences only.
11. ICS file imports into at least two mainstream calendar apps with correct times.
12. No attendee phone numbers/personal data exposed to organizers or public pages (field-level API tests).

## Communities & messaging (AC-COMM / AC-DM)
13. Join/leave/post/comment/react/report flows enforce membership server-side (BOLA suite green).
14. DMs readable only by the two participants (cross-user access tests); initiation outside allowed contexts is rejected.
15. Publication review: low-trust organizer content is not public before approval; rejection returns it to draft with a reason; notifications sent.

## Notifications & PWA (AC-NOTIF)
16. Every defined trigger creates an in-app notification; push is sent only with subscription + preference enabled; disabling a type stops its pushes.
17. App is installable (manifest + SW audit), works offline to the defined shell extent, and never shows cached private data after logout.

## Admin (AC-ADMIN)
18. Admin can manage users/events/communities/categories/cities/reports and view audit logs; every sensitive action writes exactly one append-only audit entry; non-admins get 403 on all admin routes.

## Platform (AC-PLAT)
19. Both locales fully usable RTL/LTR with no untranslated UI chrome on critical journeys; language switch preserves the equivalent page.
20. Performance: LCP ≤ 2.5s / INP ≤ 200ms / CLS ≤ 0.1 on home, event, community pages under the documented lab conditions, or explicit waiver recorded.
21. Accessibility audit (WCAG 2.2 AA) on critical journeys passes or waivers recorded; keyboard-only completion of J1–J4 demonstrated.
22. SEO: sample public pages have correct canonical/hreflang/OG/structured data; private routes are `noindex` and absent from the sitemap; sitemap contains only published content.
23. Backups: restore drill from a real backup reproduces data + media; missing-secret production boot fails with a clear message; secret scan of repo is clean.
24. No fabricated data in production paths: with SMS unconfigured, OTP request returns an honest error (never success).

## Phase 0.1 additions — packaging, authorization & event edge cases (AC-AUDIT)
25. **Application-image workflow (Phase 1, amended Phase 0.2):** the ADR-0018 selected workflow — a conventional Dockerfile by founder-approved default (D-01/Option C), or the Dockerfile-less assembly only if experiment E-1 proves it against the identical criteria — builds the app, runs it via Compose, executes one migration with the same image, and rolls back by digest. *(Superseded Phase 0.1 wording required “no Dockerfile”; founder decision D-01 lifted that constraint, so the presence of a reviewed Dockerfile is no longer a failure — an **unrecorded** workflow choice is.)* Evidence is recorded in ADR-0018; see AC-P1 7–8.
26. **Migration failure drill (Phase 1):** apply-from-empty, apply-over-previous, an induced mid-deploy migration failure (old app still serves, deploy halts), and restore-from-backup all demonstrated on a throwaway stack.
27. **Permission matrix conformance:** every DOMAIN-MODEL §11 cell is covered by an automated test, including: community managers get no rights over a linked event they don't organize; admins cannot register on behalf of users or bulk-export attendees; organizers never receive phone numbers.
28. **Attendee privacy:** anonymous responses contain counts only; `public` visibility still requires authentication; suspended users' existing sessions stop working immediately (test).
29. **Material-change notifications:** rescheduling a registered occurrence (start/location/format) notifies every confirmed registrant with old→new values and preserves registrations; a series-rule edit never moves an occurrence that has registrations.
30. **DST correctness:** recurrence expansion passes gap (nonexistent local time) and fold (ambiguous local time) tests in a DST-observing timezone, and Jalali/Gregorian rendering of the same instant agrees on ordering in both locales.
31. **Suspension/deletion semantics:** organizer suspension blocks management without auto-cancelling events; community archival converts linked events to standalone under their organizer; account deletion releases the phone for re-registration only after anonymization completes.
32. **Blocking (if D-05 approved):** a blocked user cannot initiate or send; the failure reveals nothing about the block; unblocking restores messaging; report remains available throughout.
33. **Lost-update protection:** two concurrent organizer edits to one occurrence yield one success and one `REVISION_CONFLICT` — never a silent overwrite.

## Phase 1 foundation acceptance (AC-P1) — measurable; none may be marked passed before it is tested

Status at Phase 0.2: **all AC-P1 items are untested** (no implementation exists). Phase 1's exit report must classify each as *Verified & passing / Implemented but not verified / Blocked by external resources / Deferred with explicit reason* (IMPLEMENTATION-PLAN Phase 1 exit gate).

1. **Fresh-checkout setup:** a clean clone is configured and running (dev dependencies + app) using only the documented steps, with no undocumented manual actions; divergences are corrected in the docs within Phase 1.
2. **Secrets hygiene:** secret scanning over the repo **and** the built image layers passes; no real credentials in repo or image; production boot with a required variable missing fails naming the variable (never its value); production mode with a mock provider selected is rejected.
3. **Pinned dependencies:** every direct dependency is version-pinned, the lockfile is committed, and a frozen install from the lockfile succeeds on a clean machine/CI run; Node/Nuxt/TypeScript/pnpm/PostgreSQL/Drizzle/Nuxt UI versions match the matrix recorded in ADR-0001/0003.
4. **Type & lint:** TypeScript strict type checking and the configured linter pass with zero errors on a clean checkout.
5. **Production build:** the production build completes from a clean state in CI; the artifact that is imaged is this build output.
6. **Readiness honesty:** the app reports *not ready* when PostgreSQL is unavailable and *ready* only when required dependencies respond; health output exposes no configuration values or secrets.
7. **Image reproducibility & start:** two clean image builds from the same commit both produce an image that starts and serves health/readiness (any digest difference is explained in the Phase 1 report); the runtime process runs as non-root; production Compose references the image by digest.
8. **Migrations controlled:** the minimal technical migration applies from an empty database and over the prior schema state using the same image; a deliberately failing migration halts the deploy path, leaves migration state uncorrupted (no partially recorded migrations), the previous app version still serves, and the documented forward-fix/restore procedure is executed successfully in a drill.
9. **Repeatable workflows:** documented dev and test commands each pass twice in a row on a clean environment with identical results; tests run against an ephemeral database, never a persistent developer database.
10. **Compose foundations:** Compose services define health checks; PostgreSQL and MinIO data survive `compose down`/up via named persistent volumes; database and MinIO ports are not published on a public interface.
11. **Deployment parity:** the deployment documented in DEPLOYMENT-AND-BACKUP-PLAN is the workflow actually tested (same image, same migration step, rollback-by-digest demonstrated on a scratch stack) — no undocumented steps.
12. **Scope discipline:** the Phase 1 tree contains no product features — no auth, profiles, events, communities, posts, messaging, notifications, product-domain entities/migrations, or product UI screens (a trivial framework placeholder route is not a product screen) — confirmed by exit diff review.
13. **Docs match reality:** setup/ops docs, version matrices (ADR-0001/0003), the selected workflow (ADR-0018, with E-1 evidence), and PROJECT-STATUS reflect the tested state.

## Critical journeys (must all pass as e2e, both locales where applicable)
J1 register · J2 OTP signup + onboarding · J3 join + post · J4 organizer create recurring event + cancel one occurrence · J5 DM within rules · J6 admin review + moderation (USER-FLOWS).

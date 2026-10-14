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
25. **Dockerfile-less packaging spike (Phase 1):** the ADR-0018 Option A pipeline builds `.output`, assembles the image on the digest-pinned official Node image, runs it via Compose, executes one migration with the same image, and rolls back by digest. No `Dockerfile` exists in the repo. If the spike fails, Option B is recorded in ADR-0018 instead — silently adding a Dockerfile fails this criterion.
26. **Migration failure drill (Phase 1):** apply-from-empty, apply-over-previous, an induced mid-deploy migration failure (old app still serves, deploy halts), and restore-from-backup all demonstrated on a throwaway stack.
27. **Permission matrix conformance:** every DOMAIN-MODEL §11 cell is covered by an automated test, including: community managers get no rights over a linked event they don't organize; admins cannot register on behalf of users or bulk-export attendees; organizers never receive phone numbers.
28. **Attendee privacy:** anonymous responses contain counts only; `public` visibility still requires authentication; suspended users' existing sessions stop working immediately (test).
29. **Material-change notifications:** rescheduling a registered occurrence (start/location/format) notifies every confirmed registrant with old→new values and preserves registrations; a series-rule edit never moves an occurrence that has registrations.
30. **DST correctness:** recurrence expansion passes gap (nonexistent local time) and fold (ambiguous local time) tests in a DST-observing timezone, and Jalali/Gregorian rendering of the same instant agrees on ordering in both locales.
31. **Suspension/deletion semantics:** organizer suspension blocks management without auto-cancelling events; community archival converts linked events to standalone under their organizer; account deletion releases the phone for re-registration only after anonymization completes.
32. **Blocking (if D-05 approved):** a blocked user cannot initiate or send; the failure reveals nothing about the block; unblocking restores messaging; report remains available throughout.
33. **Lost-update protection:** two concurrent organizer edits to one occurrence yield one success and one `REVISION_CONFLICT` — never a silent overwrite.

## Critical journeys (must all pass as e2e, both locales where applicable)
J1 register · J2 OTP signup + onboarding · J3 join + post · J4 organizer create recurring event + cancel one occurrence · J5 DM within rules · J6 admin review + moderation (USER-FLOWS).

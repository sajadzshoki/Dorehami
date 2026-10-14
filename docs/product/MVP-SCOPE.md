# MVP Scope — Dorehami

Status: **Confirmed** for the Included/Excluded lists below (from the Phase 0 brief). Anything not listed here is out of scope until added by a documented scope change.

## 1. Included in the MVP

1. Single user account supporting multiple participation and organizer capabilities.
2. Phone-number authentication via OTP (Kavenegar adapter; ADR-0005).
3. Optional password setup for subsequent sign-ins, with documented security policy (SECURITY-BASELINE §2).
4. User profiles, interests, city preferences, privacy settings.
5. Interest selection during onboarding.
6. Event discovery and search.
7. Filtering by category, city, time, format, and other essential attributes.
8. In-person, online, and hybrid events.
9. Independent events and community-linked events.
10. Recurring events from the initial release (series/occurrence model, ADR-0007).
11. Event capacity management and secure registration.
12. Duplicate-registration prevention.
13. Event cancellation and a clearly defined attendee cancellation policy (DOMAIN-MODEL §5).
14. Calendar integration and standard ICS export.
15. Public communities.
16. Community membership and leaving.
17. Dedicated organizer pages.
18. A simple organizer workspace for managing events and communities.
19. Publication controls, including additional review for new/lower-trust organizers.
20. Basic community posts, comments, reactions, and content reporting.
21. Basic one-to-one direct messaging with strict participant authorization.
22. In-app notifications.
23. Push notification support when configured, respecting user preferences.
24. Complete admin interface: users, events, communities, categories, cities, reports, audit logs.
25. Public, indexable SEO pages.
26. Persian/English localization with correct RTL/LTR.
27. Jalali and Gregorian calendar presentation per language/settings.
28. Light and dark themes.
29. Installable PWA support.
30. File/image storage using MinIO.
31. Automated tests for critical user journeys.
32. Deployment, backup, restore, and operational documentation.

## 2. Explicitly out of scope for the MVP

Online payments, paid tickets, refunds, financial settlements · waiting lists for full events · advertising · advanced ML recommendations · advanced organizer analytics · extensive gamification/reputation systems · complex real-time group chat · separate native Android/iOS apps · microservices · standalone search engine (e.g. Elasticsearch) unless measured requirements justify it · heavy analytics infrastructure.

**Scope guard:** introducing any item above requires a written scope-change note appended to this file plus an ADR update. "Free events only" follows from the payments exclusion: the UI must never imply paid ticketing in MVP.

## 3. Post-MVP roadmap (indicative order, Provisional)

1. Waiting lists (first candidate — directly extends capacity model).
2. Payments & paid ticketing with refunds/settlement (requires legal/financial design, new ADRs).
3. Richer recommendations (rule-based first; ML only with measured need and privacy review).
4. Organizer analytics (privacy-conscious aggregates).
5. Group chat (real-time infra decision required).
6. Reputation/gamification (only with abuse analysis).
7. Dedicated search service (only on measured PostgreSQL limits — ADR-0010 revisit trigger).
8. Native apps (only if PWA proves insufficient, measured).

## 4. Assumptions recorded (not facts)

- A-1: MVP events are free of charge (implied by payments exclusion). Validate before Phase 4 UI copy is finalized.
- A-2: Initial launch is Iran-focused (fa-first, Kavenegar, Jalali) with English fully supported. Validate (Q-08).
- A-3: Public communities only in MVP (private communities are not in the included list; membership of public communities may still be join-on-request if the founder chooses — see DOMAIN-MODEL §6, Provisional).
- A-4: One community per event maximum (an event links to at most one community). Validate (Q-06).

# Product Requirements Document — Dorehami

Status: Phase 0 baseline. Labels: **Confirmed** = stated in the Phase 0 brief; **Provisional** = recommended, needs validation; **Open** = tracked in [PROJECT-STATUS](../PROJECT-STATUS.md).

## 1. Problem

People — especially students and early-career professionals — struggle to discover relevant in-person/online events and to find communities where they can keep participating after a single event. Organizers and community founders lack one accessible place to publish events, grow a community, and manage participation. Existing options fragment discovery, registration, and community engagement.

## 2. Vision (Confirmed)

Dorehami is a bilingual platform that helps people **discover events, find people with shared interests, participate in communities, and organize experiences together**. Two equal pillars:

1. **Event discovery and participation.**
2. **Community discovery and engagement.**

An event may exist independently or belong to a community; a community may organize many events. The product must not degenerate into a mere event-listing website.

## 3. Value proposition

One accessible experience combining discovery (by interest, category, city, time, format), trustworthy participation (transparent capacity, cancellation, calendar export), and durable communities (membership, posts, organizers people can follow across events).

## 4. Target audiences (Confirmed; not limited to these)

- People looking for events matching their interests.
- University students and early-career professionals.
- People interested in technology, arts, culture, business, education, entertainment, social activities.
- Independent and professional event organizers.
- Community founders and managers.
- Organizers of in-person, online, and hybrid events.

The product must not be limited to one profession, age group, city, or category.

## 5. Core principles (Confirmed)

- Discovery is useful **before login**; public browsing requires no account.
- Authentication is required only for: event registration, joining communities, publishing content, private interactions.
- Registration is straightforward and transparent (price is out of scope — all MVP events are free to register; see MVP-SCOPE).
- Events clearly display date, time, location/format, capacity, and participation requirements.
- Understandable privacy controls; attendee visibility follows privacy settings.
- Mobile-first, usable on slow connections; keyboard navigation and accessible interactions (WCAG 2.2 AA target).
- No empty states that lead to dead ends (every empty state suggests a next action, e.g. broaden filters, follow a category).
- **No fabricated content or fake operational success in production.** Real data in all core workflows; mock providers only in dev/tests (enforced in [SECURITY-BASELINE](../security/SECURITY-BASELINE.md)).
- Registration does **not** prove attendance; no attendance inference anywhere in MVP.

## 6. Key user journeys (summary; detail in [USER-FLOWS](USER-FLOWS.md))

J1 Discover → view event → register (logged-in) → ICS export. J2 Onboard: OTP login → interests + city → personalized discovery. J3 Discover community → join → post/comment/react. J4 Organizer: gain capability → create event (incl. recurring) → manage registrations → cancel occurrence. J5 Direct message a participant within authorization rules. J6 Admin: moderate reports, manage taxonomies, review low-trust organizers, inspect audit log.

## 7. Functional requirements (MVP; numbering used by acceptance criteria)

- **FR-AUTH** Phone + OTP auth; optional password afterwards (ADR-0005); sessions; account deactivation/deletion with documented consequences (DOMAIN-MODEL §9).
- **FR-PROFILE** Profiles, interests, city preference, privacy settings; interest selection during onboarding.
- **FR-DISC** Public event/community discovery, search, filters (category, city, time, format, plus language-relevant attributes); PostgreSQL search (ADR-0010).
- **FR-EVENT** Events: standalone or community-linked; in-person/online/hybrid; recurring via series/occurrences; capacity; duplicate-registration prevention; event & attendee cancellation policies; ICS export.
- **FR-COMM** Public communities; join/leave; posts, comments, reactions; reporting.
- **FR-ORG** Dedicated organizer pages; simple organizer workspace (events, communities, registrations).
- **FR-PUB** Publication controls; extra review for new/low-trust organizers.
- **FR-DM** Basic 1-to-1 direct messaging with strict participant authorization.
- **FR-NOTIF** In-app notifications; push when configured and per user preference.
- **FR-ADMIN** Admin interface: users, events, communities, categories, cities, reports, audit logs.
- **FR-I18N** fa/en, RTL/LTR, Jalali/Gregorian presentation, language-aware shareable URLs.
- **FR-PLAT** Light/dark themes; installable PWA; MinIO media; indexable SSR public pages.

## 8. Non-functional requirements

Security baseline ([SECURITY-BASELINE](../security/SECURITY-BASELINE.md)); performance targets LCP ≤ 2.5s, INP ≤ 200ms, CLS ≤ 0.1 as **targets, not achievements** ([SEO-AND-PERFORMANCE](../architecture/SEO-AND-PERFORMANCE.md)); UTC storage; automated tests for critical journeys; deployment/backup documentation.

## 9. Product constraints (Confirmed)

- Brand name/identity must be **configurable**, never hardcoded across the codebase (ADR-0019).
- No payments in MVP. No native apps. Modular monolith on a VPS (ADR-0001/0002).
- Persian and English copy written natively per language — no literal translations (BRAND.md).

## 10. Success measures (Provisional — validate in Phase 1)

Activation: % of new users selecting interests and completing a first registration/join within 7 days. Organizer supply: published events/week and % passing review first time. Retention proxy: repeat registrations and community joins. Instrumentation must be minimal and privacy-conscious; no heavy analytics in MVP (Open Q-11).

## 11. Assumptions & open questions

See [PROJECT-STATUS](../PROJECT-STATUS.md) — notably age policy/minimum age (Q-01), community Persian term (Q-07), initial launch city/categories (Q-08), and trust-level rules (Q-05). None of these block Phase 1 foundation work.

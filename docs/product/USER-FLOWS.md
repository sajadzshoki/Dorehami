# Critical User Flows — Dorehami

Conventions: `[public]` = no account needed; `[auth]` = login required; server enforces authorization on every `[auth]` step (hiding UI is not a control). Each flow lists its failure/edge paths — empty states must always offer a next action.

## J1 — Discover and register for an event `[public → auth]`

1. Visitor lands on home/discovery (SSR, indexable) → browses/search/ filters by category, city, time window, format (in-person/online/hybrid), language-aware.
2. Opens event page: title, organizer, community (if linked), date/time in the locale calendar, location or online link policy (link revealed per organizer setting after registration — Provisional, Q-04), capacity state (available / few left / full), participation requirements.
3. Clicks Register → if anonymous, OTP login (J2 step 1) → returns to the same occurrence.
4. Confirms registration for a specific **occurrence** → server transaction checks duplicate + capacity (DOMAIN-MODEL §4) → success shows registration state + "Add to calendar" (ICS download).
5. Edge: occurrence full → clear state, suggest other occurrences/events (no waiting list in MVP). Edge: already registered → show existing registration, offer cancellation. Edge: event cancelled meanwhile → explain and suggest alternatives.
6. Attendee cancellation: from "My registrations", cancel per policy (DOMAIN-MODEL §5); capacity is freed immediately; organizer-visible counts update.

## J2 — Sign up / log in and onboard `[public → auth]`

1. Enter phone → request OTP → enter code (expiry, attempt limits, resend cooldown per SECURITY-BASELINE §2) → session created. Optional: set a password later in settings for subsequent logins (OTP remains available).
2. Onboarding: display name → select interests (min 1, Provisional min/max Q-02) → city preference → privacy defaults explained in plain language (profile visibility, attendee-list visibility).
3. Lands on discovery personalized by interests/city (ranking is simple/rule-based in MVP — no ML).
4. Failure paths: OTP expired/too many attempts → guided retry with cooldown messaging; provider failure in production → honest error, never fake success (THREAT-MODEL T-22).

## J3 — Discover and participate in a community `[public → auth]`

1. Browse/search communities (category, city, activity) → community page: purpose, managers, upcoming events, recent posts (public preview per community visibility).
2. Join `[auth]` → membership active (or pending if founder chose approval — A-3) → can post, comment, react.
3. Create post `[member]` → publication rules apply (new/low-trust review if configured) → others comment/react; anyone can report content → admin queue.
4. Leave community: immediate; user's posts remain attributed per deletion policy (DOMAIN-MODEL §9) with clear pre-leave explanation. Managers must transfer/abandon management before leaving if sole manager (Provisional rule, Q-03).

## J4 — Organizer: create and manage an event `[auth + organizer capability]`

1. Request/gain organizer capability (ADR-0006: self-service activation + verification level; admin can revoke). Organizer page is created.
2. Create event: basics (title, description, categories), format & location/online details, capacity, participation requirements, optional community link (only communities the user manages or where permitted — Provisional, Q-06), recurrence (single or series rule with end condition), cover media (MinIO upload with validation).
3. Submit → publication state: published immediately for trusted organizers; `pending_review` for new/low-trust → admin approves/rejects with reason → organizer notified.
4. Manage: view registrations (only data the organizer is authorized to see — privacy rules, THREAT-MODEL T-06), edit future occurrences, cancel a single occurrence (notify registrants) vs. cancel series (explicit separate action, notify all).
5. Edge: reducing capacity below current registrations is rejected (never silently overbook); editing a past occurrence is rejected.

## J5 — Direct message `[auth]`

1. Start conversation only where authorization allows: Provisional rule — both users share a context (same community membership or same event registration) or the recipient accepts message requests (Q-10). No arbitrary messaging to any user.
2. 1-to-1 conversation; send/read messages; report/block (blocking is MVP? — Provisional: report yes; block recorded as Open Q-10, recommended for MVP safety).
3. Non-participants can never read messages (server-side participant check on every request; THREAT-MODEL T-07).

## J6 — Administrator `[auth + admin]`

1. Sign in (admin is a protected platform role, ADR-0006) → dashboard queues: publication reviews, content reports, organizer verifications.
2. Manage users (suspend/restore with reason), events, communities, categories, cities.
3. Every sensitive action writes an append-only audit log entry (actor, action, target, minimal metadata) and requires confirmation for destructive actions.
4. Admin cannot read users' direct messages from the admin UI in MVP (privacy decision, Confirmed as Provisional recommendation — Q-12); reports include only the reported content context.

## Cross-cutting flows

- **Language switch:** fa⇄en keeps the user on the equivalent localized URL; content authored in one language is not auto-translated (Q-13).
- **Notifications:** registration confirmations, cancellations, review outcomes, replies, messages → in-app always; push only if subscribed and preference-enabled.
- **Account deletion:** settings → consequences explained (registrations cancelled, content anonymized/retained per DOMAIN-MODEL §9) → confirm → OTP/password re-auth → scheduled deletion with audit entry.

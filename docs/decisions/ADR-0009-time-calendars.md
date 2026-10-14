# ADR-0009: UTC storage, event timezones, Jalali/Gregorian presentation

- **Status:** Proposed

## Context / problem
Events are scheduled in local time, stored reliably, and displayed as Jalali (fa) or Gregorian (en) per language and user setting; recurrence must follow wall-clock time in the event's timezone.

## Options considered
1. **Store `timestamptz` UTC + IANA timezone on event/series; convert at display** with `Intl` (`fa-IR` Jalali calendar built into ICU), fallback library if verification fails.
2. Store local wall time + offset — breaks across DST/offset rule changes; rejected.
3. Jalali-native storage — corrupts ordering/interop (ICS, APIs); rejected.

## Decision
Option 1. Display policy per I18N-AND-DATE §2. **Validation (Phase 1/2):** verify `Intl` Jalali output incl. leap years and digit shaping on the pinned Node/browser matrix; if insufficient, adopt a maintained Jalali library and record it here. ICS uses UTC with timezone fidelity (Phase 6 tests).

## Display & edge rules (added Phase 0.1)

- Calendar display (Jalali vs Gregorian) is a *presentation* choice from locale + user setting; it never changes the stored instant or the event timezone used for recurrence. Both the local event time and, when the viewer's timezone differs from the event's, an unambiguous label of which timezone is shown are required on registration-critical screens.
- DST gap/fold handling for recurrence expansion is defined in ADR-0007. Date filters (`from`/`to`) are interpreted in the event timezone of each candidate occurrence, not the server's timezone.

## Consequences
All sorting/filtering is timezone-safe; date-picker UX (accessible Jalali/Gregorian) is a deliberately budgeted component, not an afterthought.

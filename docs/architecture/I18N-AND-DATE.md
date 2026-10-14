# Internationalization & Dates — Dorehami

Status: Phase 0 policy. Libraries named are **Provisional pending Phase 1 version verification** (e.g. Nuxt i18n module compatibility with Nuxt 4).

## 1. Languages & routing

- Locales: `fa` (Persian, RTL) and `en` (English, LTR). Default locale: `fa` (Provisional, A-2 — validate Q-08).
- **Shareable, language-aware routes:** every public page lives under `/fa/…` or `/en/…`; switching language navigates to the equivalent localized URL (redirect map for slugs, SEO §2). Non-prefixed URLs redirect to the negotiated default locale (cookie → Accept-Language → `fa`).
- `<html lang dir>` set per locale on the server (no client-side flip flash); layouts use logical CSS properties (Tailwind `ms-/me-/ps-/pe-/start-/end-`) — layouts must never assume LTR.
- Copy is authored natively per language (BRAND §3). Authored content (event descriptions etc.) is stored in the author's language and shown as-is in both locales — no machine translation in MVP (Q-13). UI chrome is fully translated; untranslated content is not hidden.

## 2. Dates, times, calendars

- **Storage:** UTC `timestamptz` everywhere. **Display:** converted to the event's timezone (default Provisional `Asia/Tehran`; events store an IANA timezone) and rendered in **Jalali for `fa`, Gregorian for `en`**, with a user setting to override calendar presentation (MVP scope item 27).
- Use `Intl.DateTimeFormat` with `fa-IR` / `en` calendars where platform support suffices; Phase 1 verifies Jalali correctness (incl. leap years) against a dedicated library if `Intl` proves insufficient, and records the choice in ADR-0009's validation note.
- Relative times ("in 3 days") localized; absolute date+time always shown for events (no ambiguity for registration decisions). Week start and digit shaping (Persian digits in fa UI) follow locale conventions via `Intl` — Provisional, validated in Phase 2 with native review.
- ICS export: UTC times with `DTSTAMP`, event timezone preserved via `VTIMEZONE`/UTC equivalence chosen in Phase 6 and tested against common calendar apps.

## 3. Numbers, formats, text

- Phone numbers: stored E.164; displayed localized; input accepts Persian/Arabic digits normalized server-side (validation tests required).
- Search `q` must handle Persian/Arabic character variants (ی/ي, ک/ك) — normalization in the search layer (ADR-0010).

## 4. Accessibility of i18n

Language switcher is a real navigable control with `hreflang` equivalents; mixed-direction content (e.g. English event title inside fa page) uses `dir="auto"` on user content containers; placeholders and validation errors localized (native copy, not literal translation).

## 5. Open items

Q-08 default language/launch market · Q-09 Persian typeface selection (Phase 2) · Q-13 slug-per-locale vs single slug (Phase 2/3) · calendar override UX (Phase 2).

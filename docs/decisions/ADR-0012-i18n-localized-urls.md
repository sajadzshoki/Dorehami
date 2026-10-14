# ADR-0012: Internationalization with localized, shareable URLs

- **Status:** Proposed (default locale Provisional pending D-09)

## Context / problem
fa (RTL) and en (LTR) are first-class; links must be shareable and indexable per language without duplicate-content penalties.

## Options considered
1. **Locale-prefixed paths** `/fa/…`, `/en/…`, per-locale canonical + hreflang, cookie/Accept-Language negotiation only at the unprefixed entry.
2. Query/cookie-only locale — unshareable, SEO-hostile; rejected.
3. Separate domains/subdomains per locale — ops/DNS overhead, split signals; rejected for MVP.

## Decision
Option 1 with the policies in I18N-AND-DATE and SEO-AND-PERFORMANCE §2. UI copy authored natively per locale; user content shown in its authored language (no machine translation in MVP). Layouts use logical properties only. **Validation (Phase 2):** i18n module compatibility with pinned Nuxt 4 (ADR-0001) and slug strategy (per-locale vs shared slug, D-08).

## Consequences
Routing is slightly more complex (locale-aware links everywhere) — mitigated by typed route helpers from Phase 2 on.

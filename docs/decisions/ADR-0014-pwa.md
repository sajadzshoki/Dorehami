# ADR-0014: Installable PWA with conservative caching

- **Status:** Proposed

## Context / problem
MVP requires installable PWA support and usability on slow connections — without serving stale private data or masking real failures.

## Options considered
1. **Installable PWA:** web app manifest (brand config-driven name/icons), service worker caching static assets + app shell; network-first for API/HTML; offline fallback page; runtime push via Web Push (ADR-0015 adapter, VAPID).
2. Cache-first aggressive offline app — stale capacity/registration states would mislead users; rejected.
3. No PWA — violates MVP scope; rejected.

## Decision
Option 1. Never cache authenticated API responses or private pages in the service worker; versioned caches with update-on-deploy; push only with subscription + per-type preferences and safe internal links (T-21). **Validation (Phase 6):** PWA module/tooling compatibility with pinned Nuxt 4; installability audit on Android Chrome + iOS Safari limitations documented honestly in release notes.

## Consequences
Offline = read-only shell + friendly offline states (no fake data); push infrastructure adds VAPID key management (env, operations doc).

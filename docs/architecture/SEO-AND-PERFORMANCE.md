# SEO, Accessibility & Performance — Dorehami

Status: Phase 0 requirements. Performance figures are **targets, not verified achievements**.

## 1. SEO — scope

Indexable (SSR, ADR-0013): landing, event detail, community detail, category & city discovery, organizer pages. Never indexable: auth, onboarding, organizer workspace, admin, messaging, notifications, account pages (`noindex` + auth; also excluded in robots.txt as defense-in-depth only).

## 2. Requirements

- Page-specific localized `<title>`/meta description; canonical URL per locale; `hreflang="fa|en|x-default"` pairs on every localized public page.
- Open Graph/Twitter cards with the event/community cover image (signed-URL expiry must not break crawlers — public covers get a public, cacheable variant, ADR-0011).
- XML sitemap (localized, only `published` public content, updated on publish/unpublish), robots.txt, valid `Event` structured data (schema.org) only when data is real and complete — never markup for cancelled events as if upcoming.
- **URL policy:** `/fa/events/{slug}` etc. Slugs stable; title edits don't change slugs by default; slug changes 301 to the new canonical. Deleted content → `410` (after soft-delete window) or redirect to the parent community/organizer if more helpful (decide once in Phase 4, record here); cancelled events stay reachable with a clear cancelled state (users arrive from ICS/notifications) but drop `Event` offer/registration markup; expired events remain with past-event presentation (Provisional).
- No authenticated/permission-restricted data in public HTML — SSR public queries filter `publication_status='published'` and exclude attendee identities beyond privacy-safe aggregates. Verified by tests (THREAT-MODEL T-06/T-07).

## 3. Accessibility (target WCAG 2.2 AA)

Color contrast AA for text and UI components (coral `#E56C43` on warm background must be checked — likely action-color only, not body text; Phase 2 verifies and adjusts tokens, not text color compromises); visible focus states; full keyboard navigation incl. menus, dialogs, date pickers; semantic landmarks/headings; labeled forms with localized, programmatically associated error messages; screen-reader announcements for dynamic changes (registration success, toasts); no information by color alone (capacity/format also textual); `prefers-reduced-motion` respected; accessible calendar/date selection (keyboard-operable Jalali/Gregorian picker — dedicated Phase 2/4 component task, a known hard problem, budget accordingly); touch targets ≥ 44px (Provisional, WCAG 2.2 AA AA target-size guidance).

## 4. Performance targets & validation

| Metric | Target | Measurement (Phase 8) |
|---|---|---|
| LCP | ≤ 2.5s | Lab (Lighthouse, mid-tier Android, throttled 4G) on home, event, community pages + field data if available |
| INP | ≤ 200ms | Lab interaction scripts on search/filter and registration |
| CLS | ≤ 0.1 | Lab + reserved image aspect ratios |

Budget rules: SSR public pages; image optimization via MinIO variants (responsive sizes, modern formats, lazy below fold); paginate everything list-like; no N+1 queries (query-count assertions in tests); JavaScript budget Provisional ≤ 170KB gz initial route JS (validate Phase 2); usable on slow connections — core discovery/registration flows must not depend on heavy client JS. Targets are re-baselined with real numbers in Phase 8's report; failing a target blocks the "performance" acceptance line until waived explicitly in PROJECT-STATUS.

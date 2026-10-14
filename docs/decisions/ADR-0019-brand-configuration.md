# ADR-0019: Centralized brand configuration & design tokens

- **Status:** Accepted requirement (brief); implementation Proposed for Phase 2

## Context / problem
The product name, tagline, palette, typography, and logo must be changeable centrally — renaming or re-skinning cannot be a codebase-wide hunt (BRAND §5).

## Options considered
1. **Single brand-config module + design tokens:** names/taglines (fa/en), logo asset references, color/typography tokens feeding CSS custom properties (light+dark); DB `system_settings` optional override for non-secret display strings only (decided Phase 2). Emails/SMS templates, PWA manifest, SEO defaults all read from this config.
2. Env-var-per-string config — scattered, untyped; rejected as primary.
3. Hardcoded brand strings — explicitly forbidden; rejected.

## Decision
Option 1. Initial tokens = BRAND §4 provisional palette (`#E56C43`, `#253D35`, `#F7F4ED`, `#252925`) with dark-theme tokens derived and contrast-verified in Phase 2. Components reference semantic tokens (`brand.primary`, `surface.background`), never raw hex values or the literal product name.

## Consequences
A Phase 2 lint/review rule: no hardcoded brand strings/hex colors in components. White-labeling beyond a single brand is **not** an MVP goal — config exists for changeability, not multi-tenancy.

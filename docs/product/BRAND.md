# Brand Foundation — Dorehami

Status: Phase 0 baseline. Colors and type below are **provisional tokens**, not permanent constraints.

## 1. Identity (Confirmed)

| Item | Value | Notes |
|---|---|---|
| Persian name | دورهمی | Primary name in Persian contexts. |
| English name | Dorehami | Primary name in English/Latin contexts. |
| Technical identifier | `dorehami` | Package/repo/env naming; lowercase ASCII. |
| Primary tagline | `Discover together.` | English tagline. |
| Persian brand message | «چیزهای تازه، آدم‌های تازه، دورهم.» | Persian message; not a literal translation of the tagline. |

## 2. Positioning (Confirmed)

Dorehami helps people discover meaningful experiences, explore events relevant to their interests, and find communities where they can participate and connect. Core value = event discovery + shared interests + participation + communities in one accessible experience.

## 3. Personality & voice (Confirmed)

Friendly, curious, social, energetic, welcoming, trustworthy; contemporary without feeling generic or corporate.

- **Do:** warm, plain, specific language; verbs that invite participation; transparent wording about capacity, cancellation, privacy.
- **Don't:** exaggerated marketing claims, childish language, generic AI-generated copy, bureaucratic tone.
- **Language principle:** Persian and English copy are written **natively and independently** for each language and context. Never ship literal translations. Each locale has its own microcopy for errors, empty states, and notifications (owned in i18n resources, reviewed per language — see [I18N-AND-DATE](../architecture/I18N-AND-DATE.md)).

## 4. Visual direction (Provisional)

Warm, light, modern, socially engaging. Future UI must support light **and** dark themes, correct RTL (fa) and LTR (en) layouts, and prioritize readability, information hierarchy, and real event content over decoration. Avoid generic SaaS dashboards, excessive gradients/glass effects, and decorative clutter. Animation, shadows, and cards only where they serve a clear purpose.

Initial color palette (provisional brand tokens):

| Token (semantic) | Value | Role |
|---|---|---|
| `brand.primary` (coral) | `#E56C43` | Primary actions, brand accents. |
| `brand.deep` (deep green) | `#253D35` | Headings, deep surfaces, dark-theme anchor. |
| `surface.background` (warm) | `#F7F4ED` | Light-theme app background. |
| `text.primary` | `#252925` | Primary text on light surfaces. |

Dark-theme counterparts are **not yet defined** (Open D-12): they must be derived and contrast-checked (WCAG 2.2 AA) in Phase 2, not invented here. Typography is also open (D-12): a Persian-capable font with Latin support must be selected and licensed in Phase 2 (self-hosted for performance/privacy, Provisional).

## 5. Configurable identity (Confirmed requirement — ADR-0019)

Brand name (fa/en), tagline/message, logo assets, palette, and typography are **central configuration/design tokens**, never hardcoded strings or hex values scattered through components. Concretely (Phase 2): one brand-config module + CSS custom properties generated from tokens; product name in UI chrome, emails/SMS templates, PWA manifest, and SEO defaults all read from that config. Renaming the product or re-skinning must be a config change, not a refactor.

## 6. What Phase 0 does not do

No logo is generated, no design system is built, and no UI is implemented in Phase 0. Logo/asset creation is a Phase 2 deliverable with its own review.

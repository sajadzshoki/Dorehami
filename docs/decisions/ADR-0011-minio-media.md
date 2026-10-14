# ADR-0011: MinIO storage & media-access policy

- **Status:** Proposed

## Context / problem
Avatars, event covers, community images must be stored safely: no public buckets by accident, no MIME spoofing, crawler-friendly public covers without exposing private media (T-12).

## Options considered
1. **MinIO (S3-compatible) private buckets; app-mediated uploads; short-lived signed URLs** for private media; explicitly-public derivative bucket/paths only for published covers/avatars.
2. Local filesystem on the VPS — backup/path coupling, no signing; rejected.
3. External S3/CDN from day one — cost/vendor dependency before need; adapter makes it possible later.

## Decision
Option 1, accessed through the `media` module (S3 adapter port). Upload rules in SECURITY-BASELINE §6 (images only, content-sniffed, size cap, random keys, SVG excluded in MVP). Public SEO/OG images use the public derivative path only after publication. Media rows carry status so orphan cleanup and account-deletion flows can find files (ERD).

## Consequences
Bucket policies become part of the deployment checklist + a boot-time/deploy-time verification (anonymous listing denied test). Backup includes MinIO data (operations plan).

# ADR-0018: Docker Compose deployment without custom Dockerfiles

- **Status:** Accepted constraint (brief); app-image mechanism **Open**

## Context / problem
Deploy on one VPS with official, explicitly versioned images, persistent PostgreSQL/MinIO volumes, health checks, env validation — and **no custom Dockerfiles**.

## Options considered
1. Compose stack: reverse proxy (official Caddy/Traefik image), PostgreSQL (official pinned), MinIO (official pinned), and the Nuxt app run on an **official Node image with the built app mounted/provided via a versioned artifact** (e.g. build output produced in CI and delivered as a tarball/volume, or an official image executing `node .output/server/index.mjs`). No project-authored Dockerfile anywhere.
2. Custom multi-stage Dockerfile — explicitly forbidden by the brief.
3. Bare-metal Node + systemd — contradicts the Compose requirement.

## Decision
Option 1. **Open point for Phase 1 (blocking for that phase, not for Phase 0):** choose and document exactly how the built Nuxt output reaches the official Node image reproducibly (CI artifact + Compose volume is the Provisional recommendation), including how Drizzle migrations run in that model (ADR-0004). The choice must keep image versions pinned, updates deliberate (reviewed version bumps), and secrets out of images (T-19).

## Consequences
Slightly unconventional app packaging in exchange for the brief's constraint; reverse-proxy choice (Caddy vs Traefik vs nginx official images) is a Phase 1 validation item recorded here once decided.

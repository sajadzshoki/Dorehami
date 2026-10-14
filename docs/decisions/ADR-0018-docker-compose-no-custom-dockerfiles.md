# ADR-0018: Docker Compose deployment without custom Dockerfiles

- **Status:** Accepted constraint (brief). Image-delivery mechanism: **Option A recommended (Phase 0.1), founder confirmation requested — see PROJECT-STATUS D-01.** No option below quietly violates the constraint.

## Context / problem
Deploy on one VPS with official, explicitly versioned images, persistent PostgreSQL/MinIO volumes, health checks, env validation — and **no custom Dockerfiles**. The unresolved question was how built Nuxt (Nitro) output becomes a runnable, reproducible production artifact.

## Options considered

### Option A (Recommended) — Dockerfile-less OCI image assembled in CI
CI builds the Nuxt app (`nuxt build` → self-contained `.output`), then assembles an OCI image **without authoring a Dockerfile**: start from the official Node 24 LTS image pinned **by digest**, add the `.output` (+ `public`, migrations, and a migration entrypoint) as a layer using a Dockerfile-less image tool (e.g. Google's `crane append` from go-containerregistry, or BuildKit's Dockerfile-less inputs if proven equivalent), set the entrypoint to the Nitro server (`node .output/server/index.mjs`), and push to a registry (GHCR) tagged with the git SHA. The VPS Compose file references the image **by digest**, never a floating tag.
- Pros: immutable, reproducible, rollback = previous digest; no host-side artifact handling; image contains no secrets (env injected at runtime); migration job can run the *same* image with the migration command (ADR-0004).
- Cons: less conventional than a Dockerfile; CI must pin the assembler tool and base digest; Phase 1 must prove Nitro output + migrations run correctly from the assembled image (spike, see Roadmap Phase 1).
- Constraint reading: no project-authored Dockerfile exists; the base is the unmodified official image plus an application layer. This satisfies “no custom Dockerfiles” as written.

### Option B — Official Node image + versioned build artifact on the host (fallback)
CI publishes a checksummed release tarball; the VPS extracts it to a versioned directory; Compose runs the official Node image with that directory bind-mounted read-only and `command: node …/server/index.mjs`; rollback repoints the mount/symlink.
- Pros: dead simple, transparent. Cons: artifact lives outside an image (weaker provenance/immutability), host state drifts more easily, migration execution uses the host artifact too. Acceptable fallback if Option A's Phase 1 spike fails; the failure and this fallback must be recorded here.

### Option C — Relax the constraint: one reviewed multi-stage Dockerfile (requires founder approval)
The conventional path: official base images, `deps → build → slim runtime`, Compose builds or pulls it.
- Pros: most familiar, best tooling support. Cons: **violates the current project constraint**; only adoptable by an explicit founder decision superseding this ADR's constraint. Presented for completeness — not adopted, not implemented.

### Option D — Cloud Native Buildpacks (not recommended for MVP)
`pack build` with a maintained builder produces an OCI image without a Dockerfile. Rejected for MVP: builder supply-chain and Node/Nuxt behavior are another platform to learn and pin, with no benefit over Option A for one app.

## Decision (Proposed, pending founder confirmation D-01)
Adopt **Option A**, with Option B as the documented fallback and Option C available only via founder-approved supersession. Reverse proxy: official **Caddy** image is the Phase 0.1 recommendation (automatic HTTPS, tiny config); Traefik/nginx remain acceptable Phase 1 substitutions recorded here. PostgreSQL and MinIO use official images pinned by digest (versions per ADR-0003; MinIO pinned to a named RELEASE tag + digest at bootstrap — do not float `latest`).

## Local / test / production workflow (normative)
- **Local dev:** either host Node 24 + Corepack pnpm, or the official Node image with the repo bind-mounted (dev only — bind mounts are forbidden in production). PostgreSQL/MinIO from the dev Compose using the same major versions as production.
- **Tests:** CI runs unit/integration against ephemeral PostgreSQL (service container / Compose test profile) with mock providers; e2e against the built `.output`. Test images are never the production artifact path.
- **Production build:** on tagged/merged commit → CI: install from lockfile → lint/typecheck/test → `nuxt build` → assemble image (Option A) → push by SHA → record digest.
- **Deploy:** update the digest in the production Compose env → pre-migration backup → one-shot migration run (same image, ADR-0004 failure rules) → `compose up -d` → health + smoke checks → monitor. Rollback: previous digest (+ forward-fix migration if schema changed, per expand/contract).

## Consequences
Phase 1's first deliverable is a **spike proving Option A end-to-end** (build → assemble → run → migrate → rollback-by-digest on a scratch stack). If the spike fails, Phase 1 records why and lands Option B — it may not silently write a Dockerfile. Founder confirmation of Option A (or choosing C) is tracked as D-01 in PROJECT-STATUS and is the only remaining decision blocking Phase 1's packaging work.

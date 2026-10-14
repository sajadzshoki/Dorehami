# ADR-0018: Docker Compose deployment and application-image strategy

- **Status:** Accepted — **founder decision D-01 (Phase 0.2): Option C approved.** A conventional, standard Dockerfile is permitted. The former no-custom-Dockerfile constraint is **lifted**; the Dockerfile-less approach is retained as an option only if it meets the same acceptance criteria. Final workflow is selected by the bounded Phase 1 experiment E-1 (PROJECT-STATUS) on feasibility and maintainability evidence — not ideology.
- **Decision history (preserved):** Phase 0 brief imposed “no custom Dockerfiles” → Phase 0.1 recommended Option A (Dockerfile-less OCI assembly) under that constraint → **Phase 0.2 founder approved Option C**, superseding the constraint. No Dockerfile, image, or Compose file is created in Phase 0.2; this ADR records the decision only.

## Context / problem
Deploy on one VPS with official, explicitly versioned (digest-pinned) images, persistent PostgreSQL/MinIO volumes, health checks, env validation — and a production app image that is reproducible, secure, and maintainable by a small team. The simplest reliable workflow is preferred.

## Options considered

### Option C (Approved, default candidate) — Conventional multi-stage Dockerfile
Official Node 24 LTS base images pinned by digest; a reviewed multi-stage Dockerfile (install from the committed lockfile with a frozen install → build → slim runtime containing only the Nitro `.output` + migration assets, running as a non-root user, no secrets in any layer).
- Pros: most familiar and best-supported path; standard tooling for caching, scanning, and debugging; single artifact built identically in CI and (if needed) locally.
- Cons: Dockerfile must be maintained and reviewed like code; layer hygiene (no secrets, no dev dependencies in runtime) is the team's responsibility — enforced by the validation criteria below.

### Option A — Dockerfile-less OCI image assembled in CI (retained option)
CI builds `.output`, then assembles an image without authoring a Dockerfile (`crane append` onto the digest-pinned official Node image), pushed by SHA/digest.
- Pros: no Dockerfile to maintain; immutable digest workflow. Cons: less conventional; assembler tooling and entrypoint/migration handling must be proven; debugging image contents is less familiar.
- **Retention condition:** may be selected instead of C only if experiment E-1 shows it meets *all* the same criteria (reproducible build, no secrets, starts + migrates, rollback, maintainability) with equal or lower overall complexity. “No Dockerfile” is not itself a criterion anymore.

### Option B — Official Node image + verified release tarball on the host (fallback)
Checksummed tarball bind-mounted read-only; rollback repoints the mount. Acceptable only if both C and A fail E-1; weakest provenance, host-state drift risk. The failure of C/A must be recorded here if B is ever adopted.

### Option D — Cloud Native Buildpacks (not recommended for MVP)
Rejected for MVP: another platform/supply chain to pin and learn, with no demonstrated benefit over C for one app. Revisit only via a new experiment record.

## Decision
**Option C is approved and is the default candidate.** Phase 1 opens with experiment **E-1**: implement the standard Dockerfile for the scaffolded app and verify the criteria below. Option A is attempted only if C fails a criterion, or if the team still prefers it and can show in E-1 that it meets every criterion with equal-or-lower complexity. The selected workflow, its evidence, and the rejected alternative's results are recorded in this ADR at Phase 1 exit; until then the choice is “C by default, evidence-confirmed”, not an assertion that C has been tested (it has not — no build exists yet).

## Validation requirements (E-1 success criteria — all must pass for the selected workflow)
1. Base images referenced by **digest** (official Node 24 LTS line per ADR-0001); no floating tags anywhere in the production path.
2. Dependencies installed from the committed lockfile in frozen mode; a second clean build from the same commit produces a working image (image digest may differ only for documented, non-functional reasons — any difference is explained, not ignored).
3. Image contains **no secrets**: env/secret scan of image layers and repo passes; runtime config arrives via environment only.
4. Runtime image runs as non-root, starts the Nitro server, and serves the health endpoint; image size and build time are recorded (no target invented in advance — recorded for the Phase 1 report).
5. The **same image** executes the migration command successfully (ADR-0004), and a failed migration follows the documented halt/forward-fix/restore path.
6. Rollback = deploy the previous image digest; demonstrated once on a scratch stack.
7. The documented deployment path (DEPLOYMENT-AND-BACKUP-PLAN §4) is exactly the tested path — no undocumented manual steps.

## Local / test / production workflow (normative)
- **Local dev:** host Node 24 + Corepack pnpm, or the official Node image with the repo bind-mounted (dev only). PostgreSQL/MinIO from the dev Compose using the same major versions as production.
- **Tests:** CI runs unit/integration against ephemeral PostgreSQL with mock providers; the production image is built in CI and smoke-tested before any deploy.
- **Production build:** committed lockfile → lint/typecheck/test → image build (selected workflow) → push by git SHA → deploy by digest.
- **Deploy:** record digest → pre-migration backup → one-shot migration from the same image (ADR-0004 failure rules) → `compose up -d` → health + smoke checks → monitor. Rollback: previous digest (+ forward-fix migration if schema changed).

## Consequences
The packaging risk from Phase 0.1 (unproven Dockerfile-less assembly as the only permitted path) is retired: Phase 1 may use the standard, simplest reliable workflow, with the alternative kept honest by identical criteria. Costs accepted: the Dockerfile becomes maintained production code (review, pinning, secret hygiene), and E-1 evidence must be written back into this ADR before Phase 2 begins (Phase 1 exit gate).

# ADR-0002: Modular monolith over microservices

- **Status:** Accepted (Phase 0 brief: microservices explicitly out of scope)

## Context / problem
Dorehami needs transactional integrity (registration capacity), a small-team operable deployment, and room to grow into a larger social platform.

## Options considered
1. **Modular monolith** — one Nuxt deployable, domain modules with enforced boundaries (ARCHITECTURE §3).
2. Microservices — independent scaling/teams; costs: distributed transactions for capacity, multi-service ops on one VPS, premature.
3. Unstructured monolith — fastest start; boundaries erode, testing and growth suffer.

## Decision
Option 1. Modules communicate via service interfaces and in-process domain events; infrastructure behind ports.

## Rationale / consequences
Single-process transactions make ADR-0008 trivially correct; one Compose stack to back up and deploy. Trade-off: scaling is vertical first — acceptable for MVP; module boundaries keep a future split (e.g. notifications) possible without committing to it. Revisit only with measured load or team-size evidence, via a new ADR.

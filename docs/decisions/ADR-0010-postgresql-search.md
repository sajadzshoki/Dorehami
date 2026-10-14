# ADR-0010: PostgreSQL search for the MVP

- **Status:** Accepted for MVP (brief); revisit triggers below

## Context / problem
Discovery search over events/communities in Persian + English. Brief excludes a standalone search engine unless measured requirements justify it.

## Options considered
1. **PostgreSQL:** `ILIKE`/trigram (`pg_trgm`) + full-text where language config fits, with Persian/Arabic character normalization (ی/ي, ک/ك, ZWNJ handling) and indexes on title/description; filters as indexed WHERE clauses.
2. Elasticsearch/OpenSearch — powerful, but an extra stateful service to deploy/back up/secure on one VPS.
3. Client-side search — doesn't scale, leaks data; rejected.

## Decision
Option 1. Phase 4 documents the exact query shapes and index set, and load-tests them (T-20 budgets).

## Revisit triggers (any one reopens this ADR)
p95 search latency > 300ms at agreed production-like volume; corpus > ~100k searchable events with relevance complaints validated by user research; need for typo-tolerance/faceting beyond what pg_trgm delivers measurably. Absent a trigger, adding a search service is out of scope.

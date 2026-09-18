# EventEase — Backend execution plan

Repository: local `../BE-EventEase/`, with remote GitHub URL still `NEEDS DECISION`. The latest user decision selects FastAPI, a modular monolith, Supabase PostgreSQL, Render Docker deployment, and JWT. Preserve the wire contract in `../shared/API.md`. This document covers BE only; the local project is intentionally not Git-initialized yet.

## Strategy

Create a small HTTP service with modules for auth, needs/events, matching, requests, and verification/reliability. Keep one database schema and transactional state updates; do not introduce queues or microservices. Use a reproducible seed with two demo users, an organizer, an upcoming event, and a past confirmed request for post-event verification. Mark all demo venue claims as `demo`; a DKI import is optional and must keep provenance.

Matching is deterministic, versioned, and tested with known claims/needs. Since the exsum gives no numeric AHP weights, use the explicitly provisional configuration in `../shared/ARCHITECTURE.md` until the team supplies real weights. Implement unknown values conservatively and expose breakdown. Reliability uses the documented latest-N aggregation, count, and null for no data. Enforce role and ownership at every write and read; keep request/verification transitions atomic.

## Order and dependencies

1. **BE-001:** service/database skeleton, demo auth, schema, seed; can start alongside FE-001 after contract review.
2. **BE-002:** need profile and event read/create APIs. HARD on BE-001.
3. **BE-003:** match calculation and endpoint. HARD on BE-002; can be coded against fixtures while DB adapters mature.
4. **BE-004:** request submission/list/response/confirm state machine. HARD on BE-002, SOFT on BE-003; matching does not gate request persistence.
5. **BE-005:** post-event verification, reliability calculation, organizer profile. HARD on BE-004.
6. Join INT-002/003/004 as routes pass contract tests; keep BE testable without FE.

BE-003 and BE-004 can proceed in parallel after BE-002. The critical path and 8h/4h/2h cut lines are in `../shared/IMPLEMENTATION.md`.

## BE verification and fallback

Exercise the HTTP contract with a temporary database and seeded accounts. Include score edge cases (all known, partial, unknown, no required flags), unauthorized role/owner, duplicate request, invalid transition, early verification, and repeat verification. Validate migration/seed reset from a clean database. If DKI data or LLM service is unavailable, keep reviewed demo seeds and checklist input. If PostgreSQL hosting fails, a local PostgreSQL container or instance is a demo fallback; do not silently switch persistence semantics after FE integration.

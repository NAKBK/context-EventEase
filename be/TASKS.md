# EventEase — Backend tasks

Repository for every task: **backend** (local project `../BE-EventEase/`; remote URL `NEEDS DECISION`). Branch names below describe the future Git workflow. For this BE-001 local implementation, the user's latest instruction explicitly says not to run `git init`, create a branch, or commit. Global DoD: `../shared/AGENTS.md`. API IDs refer to `../shared/API.md`. BE must be runnable and testable independently of FE.

## BE-001 — Service, schema, demo auth, seed

- **Branch:** `feature/BE-001-service-seed`
- **Owner:** BE developer or AI agent
- **Priority:** P0
- **Dependencies:** INT-001 (SOFT)
- **Can run in parallel:** Yes, with FE-001
- **Local status:** Implemented; automated migration, seed, login, and bearer checks pass. Live PostgreSQL and Render deployment remain unverified.

**Goal:** establish a reproducible REST/data base.

### Scope

- choose familiar BE framework.
- PostgreSQL connection/migration.
- entities in architecture.
- two demo users/roles.
- seeded organizer, two Jakarta events (one upcoming, one past) and confirmed past request.
- demo login token.

### Outside scope

- public signup, OAuth, production user management, DKI import.

### Implementation steps

1. inspect actual repo.
2. create minimal service.
3. schema/migrations.
4. seed/reset.
5. role token middleware.
6. BE-API-001.
7. environment example.

### API and context

- BE-API-001 and global rules; `../shared/ARCHITECTURE.md` entities.

### Acceptance criteria

- clean database bootstraps repeatedly.
- login returns role token.
- seed has source=`demo` and supports full journey.

### Definition of done

- global DoD, no secrets committed, schema documented in code.

### Verification

- clean database migration/seed twice.
- HTTP login both roles and invalid account.
- auth rejection with bad token.

## BE-002 — Need profile and event APIs

- **Branch:** `feature/BE-002-needs-events`
- **Owner:** BE developer or AI agent
- **Priority:** P0
- **Dependencies:** BE-001 (HARD)
- **Can run in parallel:** Yes, with FE-002/003
- **Local status:** Implemented; needs/events endpoints, role checks, and coordinate validation pass automated tests (`tests/test_be002.py`). Fixed post-merge: needs endpoints now correctly restricted to attendee bearer, and organizer registration (BE-006) auto-provisions an Organizer profile so real accounts can publish events.

**Goal:** persist functional needs and organizer claims.

### Scope

- attendee GET/PUT needs.
- role-scoped event list/detail.
- organizer event creation.
- query validation and provenance.
- optional `lat`/`lng` on `Venue`, for later map rendering (P2, FE-007): nullable columns, accepted on event creation, returned in venue objects, range-validated when present.

### Outside scope

- matching, import, route graph, ticketing.
- routing, distance calculation, live navigation from coordinates.

### Implementation steps

1. implement data access and validation.
2. exact seven-key profile.
3. event and claim creation transaction.
4. ownership from token.
5. list/detail projections.
6. migration adding nullable `lat`/`lng` columns on `Venue`; accept them optionally on `POST /api/events`; return them (or `null`) on list/detail/create; validate `lat` in [-90,90] and `lng` in [-180,180] when present, else 422.

### API and context

- BE-API-002–005, 007; venue coordinate extension in `../shared/API.md`.

### Acceptance criteria

- profile round-trips.
- two seeded events list.
- organizer creates event and attendee reads it.
- invalid claims/dates and wrong role use contract error envelope.
- a venue with coordinates round-trips; a venue without coordinates returns `null`, not an error; an out-of-range coordinate returns 422.

### Definition of done

- global DoD, no unauthenticated write, no invented claim value, no coordinate ever fabricated for a venue that lacks real data.

### Verification

- HTTP success and 401/403/404/422 cases.
- database round-trip and clean seed check.
- HTTP create/read with and without coordinates; invalid coordinate range rejected with 422.

## BE-003 — Weighted match engine

- **Branch:** `feature/BE-003-matching`
- **Owner:** BE developer or AI agent
- **Priority:** P0
- **Dependencies:** BE-002 (HARD)
- **Can run in parallel:** Yes, with BE-004 and FE-002

**Goal:** provide explainable, deterministic match results.

### Scope

- seven-attribute weights, fulfillment conversion, unknown policy, 0–100 score, versioned breakdown and endpoint.

### Outside scope

- supervised ML, unverified AHP claim, venue route selection.

### Implementation steps

1. put provisional weights/thresholds in one versioned config.
2. calculate from stored needs and event claim.
3. return all seven breakdown items.
4. return missing-profile conflict.

### API and context

- BE-API-006; formula in architecture.

### Acceptance criteria

- score follows documented formula.
- changing profile changes result.
- unknowns return `null` fulfillment and list entry.
- all seven breakdown items present.

### Definition of done

- global DoD, no claim of validated AHP weights.

### Verification

- meaningful table tests for full/partial/unknown and short/moderate/any distance.
- HTTP 200/401/404/409.

## BE-004 — Accessibility request lifecycle

- **Branch:** `feature/BE-004-commitment`
- **Owner:** BE developer or AI agent
- **Priority:** P0
- **Dependencies:** BE-002 (HARD); BE-003 (SOFT)
- **Can run in parallel:** Yes, with BE-003 and FE-004

**Goal:** persist two-way organizer commitment.

### Scope

- request create/list, needs snapshot, organizer response, attendee confirmation/decline, state/role/owner validation.

### Outside scope

- chat, notification, payment, verification.

### Implementation steps

1. implement one-active-request rule.
2. snapshot profile.
3. role-scoped list.
4. atomic pending→responded/closed→confirmed/closed transitions.
5. immutable response note.

### API and context

- BE-API-008–011.

### Acceptance criteria

- each role sees only its relevant requests.
- written response survives reload.
- cannot-fulfill closes.
- duplicate/invalid transitions return 409.

### Definition of done

- global DoD, private notes only to participants.

### Verification

- HTTP two-role path, wrong owner 403, duplicate 409, race/transition tests using database transaction.

## BE-005 — Post-event verification and reliability

- **Branch:** `feature/BE-005-reliability`
- **Owner:** BE developer or AI agent
- **Priority:** P0
- **Dependencies:** BE-004 (HARD)
- **Can run in parallel:** Yes, with FE-005

**Goal:** compare promise with actual experience and update organizer feedback indicator.

### Scope

- all-seven verification, event-end and confirmation guard, unique submission, rolling latest-20 aggregation, organizer profile response.

### Outside scope

- fraud detection, moderation queue, ML, external certification.

### Implementation steps

1. persist one verification per request.
2. enforce completion time.
3. calculate 1/0.5/0 average across all seven values and latest 20 verifications.
4. set null score for no samples.
5. expose score and sample count.

### API and context

- BE-API-012–013; reliability rule in architecture.

### Acceptance criteria

- past confirmed request verifies once.
- score/count update.
- no verification produces null/0.
- early/duplicate/wrong owner fail with contract codes.

### Definition of done

- global DoD and reproducible seed.

### Verification

- calculation tests for full/partial/none and zero samples.
- HTTP 201/401/403/409.
- organizer profile before/after submission.

## BE-006 — Real registration and login

- **Branch:** `feature/BE-006-real-auth`
- **Owner:** BE developer or AI agent
- **Priority:** P1
- **Dependencies:** BE-001 (HARD)
- **Can run in parallel:** Yes, with BE-007/009/010
- **Local status:** Implemented; migration, register, login, and existing demo-login/bearer checks pass.

**Goal:** allow real user signup/login alongside demo accounts.

### Scope

- registration (email + password) and role selection.
- password hashing.
- login endpoint issuing the same JWT shape as demo-login.
- demo-login keeps working unchanged.

### Outside scope

- OAuth/social login, email verification, password reset, MFA.

### Implementation steps

1. add `password_hash` to the `User` model and migration.
2. `POST /api/auth/register` with email/password/role validation and uniqueness check.
3. `POST /api/auth/login` verifying the hash and issuing the standard bearer token.
4. leave `/api/auth/demo-login` untouched.
5. minimal brute-force guard is optional, not required.

### API and context

- BE-API-014, BE-API-015.

### Acceptance criteria

- a new account can register then log in.
- wrong password returns 401.
- duplicate email returns 409/422.
- a real login token works on protected routes exactly like a demo token.

### Definition of done

- global DoD, passwords never logged or stored in plaintext, no secrets committed.

### Verification

- HTTP register/login success and failure cases.
- confirm the stored value is a hash, not plaintext.
- existing demo-login tests still pass.

## BE-007 — Jakarta open data import

- **Branch:** `feature/BE-007-dki-import`
- **Owner:** BE developer or AI agent
- **Priority:** P1
- **Dependencies:** BE-002 (HARD)
- **Can run in parallel:** Yes, with BE-006/009/010

**Goal:** import a validated subset of DKI Jakarta open venue/event data with provenance.

### Scope

- fetch/parse a chosen DKI open-data source.
- map fields to `Venue`/`Event`/`Claim`.
- tag imported rows `source=open_data` with `checked_at`.
- organizer/admin-triggered import endpoint.
- idempotent re-run by external dataset ID.

### Outside scope

- automatic scheduling/cron, full dataset coverage, ML-based field mapping.

### Implementation steps

1. pick a concrete DKI dataset URL/format.
2. write the mapping to `Venue`/`Event`/`Claim` shapes, `null` for unmapped fields.
3. import endpoint (or CLI command) that triggers the run.
4. dedupe by external ID on re-run.
5. never guess an unmapped claim value.

### API and context

- BE-API-016.

### Acceptance criteria

- an import run creates venues/events with `source=open_data`.
- re-running the same import does not duplicate rows.
- unmapped fields are `null`, not guessed.

### Definition of done

- global DoD, provenance visible on every imported record.

### Verification

- import against a sample/mock dataset file.
- run twice and confirm idempotency.
- spot check one mapped and one unmapped field.

## BE-009 — Claim evidence media upload

- **Branch:** `feature/BE-009-media-upload`
- **Owner:** BE developer or AI agent
- **Priority:** P2
- **Dependencies:** BE-002 (HARD)
- **Can run in parallel:** Yes, with BE-006/007/010

**Goal:** let organizers attach photo evidence to accessibility claims.

### Scope

- multipart upload endpoint.
- Supabase Storage as the file backend, behind a thin storage client (`app/core/storage.py`).
- one-to-many media items per event.
- media list surfaced in event detail.
- content-type/size validation.

### Outside scope

- video, image moderation/ML content check, public unauthenticated access to raw files, editing existing media.

### Implementation steps

1. use Supabase Storage via `app/core/storage.py` (`SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `SUPABASE_STORAGE_BUCKET` env vars; bucket created as Public in the Supabase dashboard).
2. `POST /api/events/{event_id}/media` multipart endpoint, owning organizer only.
3. persist media metadata (`url`, `uploaded_at`, uploader) linked to the event; `url` is the Supabase Storage public URL.
4. include the `media` array in the BE-API-005 event detail response.
5. validate content-type (image only) and max size.

### API and context

- BE-API-017; extends BE-API-005 response with a `media` array.
- Env/config: `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `SUPABASE_STORAGE_BUCKET` in `.env.example`.

### Acceptance criteria

- an organizer uploads an image and it appears in event detail.
- a non-owner upload is rejected with 403.
- an oversized or wrong-type file is rejected with 422.

### Definition of done

- global DoD, an uploaded photo is never treated as an accessibility certification.

### Verification

- HTTP multipart upload success/failure cases.
- event detail includes the media item after upload.

## BE-010 — Advanced event search

- **Branch:** `feature/BE-010-search-refinements`
- **Owner:** BE developer or AI agent
- **Priority:** P2
- **Dependencies:** BE-003 (SOFT)
- **Can run in parallel:** Yes, with BE-006/007/009
- **Local status:** Implemented; exact-match facility filters, date range, and `sort=match_score` (reusing BE-003's `_compute_match`) pass automated tests (`tests/test_be010.py`). Unknown query keys now correctly return 422 instead of being silently ignored, closing a pre-existing gap in BE-API-004 itself.

**Goal:** let attendees find events matching more specific criteria than status/text.

### Scope

- extend `GET /api/events` with per-attribute claim filters.
- `date_from`/`date_to` range.
- `sort=starts_at|match_score`, the latter for authenticated attendees with a saved profile.

### Outside scope

- full-text relevance ranking, saved searches, notifications on new matches.

### Implementation steps

1. add optional query params (need-attribute filters, `date_from`, `date_to`, `sort`).
2. apply claim-based filtering server-side.
3. compute match score for sorting only when the request has an attendee bearer with a saved profile; otherwise fall back to `starts_at`.
4. an unsupported filter combination returns 422, not 500.

### API and context

- BE-API-018 (extends BE-API-004).

### Acceptance criteria

- filtering by a required attribute excludes events whose claim is `0` or `null` for that attribute.
- `sort=match_score` orders by the BE-API-006 formula.
- an invalid combination returns 422 with the contract error envelope.

### Definition of done

- global DoD, a filter never approximates what an `unknown` claim cannot support.

### Verification

- HTTP query combinations.
- ordering assertions against known seeded claims.

## BE-011 — Attendee dashboard summary

- **Branch:** `feature/BE-011-attendee-dashboard`
- **Owner:** BE developer or AI agent
- **Priority:** P2
- **Dependencies:** BE-002 (HARD); BE-003 (HARD); BE-004 (HARD)
- **Can run in parallel:** Yes, with BE-007/009
- **Local status:** Implemented; composes existing `accessibility_requests`/`events` reads and the BE-API-006 match formula only, no new business logic, pass automated tests (`tests/test_be011.py`).

**Goal:** let the attendee dashboard screen load in one call instead of an N+1 fan-out over existing endpoints.

### Scope

- one read-only endpoint composing existing services: pending-request count, the single "active" upcoming event (if any) with its match score, and a capped recent-requests list.
- reuses the BE-API-006 match formula and the BE-API-009 request item shape exactly; introduces no new scoring or request logic.

### Outside scope

- an organizer-facing dashboard.
- any personal, cross-event "accessibility score" independent of one specific event.
- real-time/push updates.

### Implementation steps

1. new module composing `accessibility_requests`, `events`, and the match-scoring path — no duplicated business logic.
2. define "active event" as: among the attendee's `confirmed` requests, the one whose event is still `upcoming`, soonest `starts_at` first; a `confirmed` request whose event has since completed is excluded (it belongs to verification, not "next event").
3. mount at `GET /api/me/dashboard`, attendee bearer only.

### API and context

- BE-API-019.

### Acceptance criteria

- no requests at all → `pending_requests_count:0`, `active_event:null`, `recent_requests:[]`.
- `active_event.match.score` is identical to calling BE-API-006 directly for the same event and attendee.
- a `confirmed` request on a now-completed event never appears as `active_event`.
- organizer bearer → 403 `FORBIDDEN`.

### Definition of done

- global DoD; this endpoint never computes a score or request shape independently — it only calls the existing implementations.

### Verification

- HTTP: empty state, pending-only, confirmed-on-upcoming with score cross-check, confirmed-on-completed exclusion, multiple-confirmed picks soonest, organizer 403.

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

**Goal:** persist functional needs and organizer claims.

### Scope

- attendee GET/PUT needs.
- role-scoped event list/detail.
- organizer event creation.
- query validation and provenance.

### Outside scope

- matching, import, route graph, ticketing.

### Implementation steps

1. implement data access and validation.
2. exact seven-key profile.
3. event and claim creation transaction.
4. ownership from token.
5. list/detail projections.

### API and context

- BE-API-002–005, 007.

### Acceptance criteria

- profile round-trips.
- two seeded events list.
- organizer creates event and attendee reads it.
- invalid claims/dates and wrong role use contract error envelope.

### Definition of done

- global DoD, no unauthenticated write, no invented claim value.

### Verification

- HTTP success and 401/403/404/422 cases.
- database round-trip and clean seed check.

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

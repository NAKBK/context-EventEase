# EventEase — Cross-repository task board

All task IDs are unique across this file, `fe/TASKS.md`, and `be/TASKS.md`. This file is the master index and contains the detailed shared/integration/QA tasks. The FE and BE files contain their own detailed tasks. Every implementation task uses one feature branch in its owning repository; when an integration task needs both repositories, create one branch per repository with the same INT ID and separate PRs.

## Master index

| ID | Repository | Priority | Owner | Dependency | Type | Parallel |
| --- | --- | --- | --- | --- | --- | --- |
| INT-001 | shared docs | P0 | Tech lead | none | none | yes |
| FE-001–FE-005 | frontend | P0 | FE developer | see `fe/TASKS.md` | mixed | yes with BE |
| BE-001–BE-005 | backend | P0 | BE developer | see `be/TASKS.md` | mixed | yes with FE |
| INT-002 | frontend + backend | P0 | Integration owner | FE-002, BE-003 | INTEGRATION | no |
| INT-003 | frontend + backend | P0 | Integration owner | FE-003, FE-004, BE-004 | INTEGRATION | no |
| INT-004 | frontend + backend | P0 | Integration owner | FE-005, BE-005, INT-002, INT-003 | INTEGRATION | no |
| INF-001 | backend + frontend | P0 | DevOps/demo owner | BE-001, FE-001 | SOFT | yes |
| QA-001 | frontend + backend | P0 | QA/demo owner | INT-004, INF-001 | HARD | no |

## INT-001 — Contract and decision freeze

- **Repository:** shared docs
- **Branch:** `feature/INT-001-contract-freeze` in the documentation home repository
- **Owner:** Tech lead
- **Priority:** P0
- **Dependencies:** None
- **Can run in parallel:** Yes, with repository inspection

**Goal:** settle open decisions that materially affect P0.

### Scope

- repository URLs, BE stack, demo auth, seed provenance, provisional weights and walking thresholds, `API.md` revision.
- publish the same revision to both teams.

### Outside scope

- implementing app code or optional integrations.

### Implementation steps

1. review exsum claims.
2. confirm/mark each open decision.
3. resolve contract changes in `API.md`.
4. communicate the revision and fixture examples.

### API and context

- all `BE-API-*`, `PROJECT.md` and `ARCHITECTURE.md`.

### Acceptance criteria

- FE and BE owners can implement without guessing payloads or states.

### Definition of done

- decision record embedded in the affected docs, no contradictory P0 rule, reviewable diff.

### Verification

- cross-read every FE endpoint against BE owner and run the consistency checks at end of this file.

## INT-002 — Integrate needs, events, and match

- **Repository:** frontend + backend
- **Branch:** `feature/INT-002-match-integration` in each
- **Owner:** Integration owner
- **Priority:** P0
- **Dependencies:** FE-002 and BE-003 (INTEGRATION)
- **Can run in parallel:** No

**Goal:** replace FE fixtures for need/event/match.

### Scope

- environment URL, auth header, adapter mapping, unknown/error states.

### Outside scope

- request/verification UI and algorithm redesign.

### Implementation steps

1. point FE client to BE.
2. compare request/response fixtures.
3. correct implementation drift without silently changing contract.
4. smoke two profiles on two events.

### API and context

- BE-API-001–006.

### Acceptance criteria

- score and breakdown come from BE and change with need profile.
- unknowns are visible.

### Definition of done

- global DoD in `AGENTS.md`, FE/BE PRs reviewable.

### Verification

- integrated browser journey plus BE HTTP tests for 200/401/404/validation.

## INT-003 — Integrate event publishing and commitment

- **Repository:** frontend + backend
- **Branch:** `feature/INT-003-commitment-integration` in each
- **Owner:** Integration owner
- **Priority:** P0
- **Dependencies:** FE-003, FE-004, and BE-004 (INTEGRATION)
- **Can run in parallel:** No

**Goal:** organizer publishes claims and both roles see one request lifecycle.

### Scope

- event creation, request submit/list, response, attendee confirmation.

### Outside scope

- post-event verification.

### Implementation steps

1. connect forms.
2. verify owner checks.
3. seed role accounts.
4. test each state transition and cannot-fulfill closure.

### API and context

- BE-API-001, 005, 007–011.

### Acceptance criteria

- organizer response and attendee confirmation persist and are visible after reload.
- unauthorized role fails.

### Definition of done

- global DoD, no private note exposure.

### Verification

- browser two-role flow and direct HTTP 403/409 checks.

## INT-004 — Integrate verification and reliability

- **Repository:** frontend + backend
- **Branch:** `feature/INT-004-verification-integration` in each
- **Owner:** Integration owner
- **Priority:** P0
- **Dependencies:** FE-005, BE-005, INT-002, and INT-003 (INTEGRATION)
- **Can run in parallel:** No

**Goal:** close Need→Match→Commit→Verify→Improve.

### Scope

- past-event verification submission, organizer score and sample count refresh.

### Outside scope

- ML and spatial routing.

### Implementation steps

1. connect verification form and organizer profile.
2. submit once.
3. refresh.
4. test duplicate/early submission failures.

### API and context

- BE-API-012–013.

### Acceptance criteria

- verified request shows once.
- reliability visibly updates with sample count.

### Definition of done

- global DoD, reproducible demo seed.

### Verification

- integrated end-to-end smoke and BE calculation tests.

## INF-001 — Demo environment and seed

- **Repository:** backend + frontend
- **Branch:** `feature/INF-001-demo-environment` in each repository that needs a code/config change
- **Owner:** DevOps/demo owner
- **Priority:** P0
- **Dependencies:** BE-001 and FE-001 (SOFT)
- **Can run in parallel:** Yes

**Goal:** make the same demo reproducible.

### Scope

- environment example, database bootstrap/reset, demo accounts and two events, deployment or local run steps in existing project docs.

### Outside scope

- production operations.

### Implementation steps

1. wire environment.
2. seed records with `demo` provenance.
3. run FE/BE with chosen host.
4. document reset and fallback.

### API and context

- all; `PROJECT.md` seed requirement.

### Acceptance criteria

- another developer launches the demo from a clean checkout.

### Definition of done

- no committed secrets, global DoD.

### Verification

- clean start and one seed reset.

## QA-001 — Final acceptance and rehearsal

- **Repository:** frontend + backend
- **Branch:** `feature/QA-001-demo-validation` in the repository where the checklist is tracked, plus a matching branch in the other repository if a code fix is needed
- **Owner:** QA/demo owner
- **Priority:** P0
- **Dependencies:** INT-004 and INF-001 (HARD)
- **Can run in parallel:** No

**Goal:** demonstrate reliable core value.

### Scope

- full two-role journey, unknown claims, network error, role denial, duplicate verification, two rehearsals.

### Outside scope

- new features.

### Implementation steps

1. reset seed.
2. run P0 path.
3. record defects by task ID.
4. fix only blockers.
5. rehearse twice.

### API and context

- all P0 endpoints.

### Acceptance criteria

- minimum demo in `PROJECT.md` completes twice without manual database edits.

### Definition of done

- global DoD, known limitations disclosed.

### Verification

- signed-off checklist with time and environment.

## Consistency check

Before freeze: all P0 product features map to FE/BE/INT IDs; all FE-used `BE-API-*` IDs exist in `shared/API.md`; FE and BE agree on revision, enums, JSON examples, and error envelope; every task has owner, repository, branch, dependency type, parallel status, acceptance, DoD, and verification. `shared/IMPLEMENTATION.md` owns the critical path and cut line.

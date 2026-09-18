# EventEase — Frontend tasks

Repository for every task: **frontend** (URL `NEEDS DECISION`). Each task is one branch. Global DoD: `../shared/AGENTS.md`. API IDs refer to `../shared/API.md`. A SOFT BE dependency permits contract fixtures; it is never a reason to leave FE idle. Do not implement BE logic here.

## FE-001 — App shell, demo entry, API adapter

- **Branch:** `feature/FE-001-app-shell`
- **Owner:** FE developer or AI agent
- **Priority:** P0
- **Dependencies:** INT-001 (SOFT)
- **Can run in parallel:** Yes, with BE-001

**Goal:** create the reusable FE base and two-role demo entry.

### Scope

- app routes/layout, navigation, role-aware session state, API client, fixture adapter, common loading/error/empty components.

### Outside scope

- business pages and backend auth implementation.

### Implementation steps

1. inspect actual repo conventions.
2. scaffold only missing pieces.
3. implement demo login adapter and fixture.
4. define typed API models/error envelope.
5. wire role-aware navigation.
6. expose fixture/real selection by environment.

### API and context

- BE-API-001 and global conventions.

### Acceptance criteria

- both demo roles can enter their own navigation.
- fixture and real adapters have the same interface.
- token is sent only to configured API.
- failed login is visible.

### Definition of done

- global DoD, no committed credential or unused dependency.

### Verification

- run repository build/lint if available.
- open both roles and test login success/failure with fixture.
- check keyboard navigation and a real 401 mapping.

## FE-002 — Attendee needs and event matching

- **Branch:** `feature/FE-002-attendee-match`
- **Owner:** FE developer or AI agent
- **Priority:** P0
- **Dependencies:** FE-001 (HARD); BE-002 and BE-003 (SOFT)
- **Can run in parallel:** Yes, with BE-002/003 and FE-003/004/005 after FE-001

**Goal:** show a personalized, explainable event decision.

### Scope

- seven-dimension needs form.
- event list/detail.
- match score and seven-row breakdown.
- unknown/provenance labels.
- loading/empty/error states.

### Outside scope

- organizer claims editing, maps, route prediction, direct FE score calculation.

### Implementation steps

1. build checklist and validation.
2. save/load profile.
3. list/open events.
4. call match endpoint.
5. render score, per-attribute fulfillment, unknowns and source/date.
6. use exact contract fixtures until BE is available.

### API and context

- BE-API-002–006, `../shared/ARCHITECTURE.md` scoring meanings.

### Acceptance criteria

- two fixture profiles produce different displayed results.
- all seven fields save/reload.
- unknown claim remains visibly unknown.
- missing profile and 401/404 have useful recovery.

### Definition of done

- global DoD, fixture shapes match API.

### Verification

- component/manual paths for form values and keyboard use.
- fixture success/error tests.
- browser check of two events and two profiles.
- later INT-002 real API smoke.

## FE-003 — Organizer event publishing

- **Branch:** `feature/FE-003-organizer-events`
- **Owner:** FE developer or AI agent
- **Priority:** P0
- **Dependencies:** FE-001 (HARD); BE-002 (SOFT)
- **Can run in parallel:** Yes, with BE and other FE page tasks

**Goal:** let organizer publish current event claims.

### Scope

- organizer dashboard/event list, event creation form, all seven claim inputs, source and unknown explanation, validation and submission feedback.

### Outside scope

- venue route editor, file upload, public claim certification, backend event persistence.

### Implementation steps

1. build organizer event list.
2. create form for title/dates/venue/claims.
3. allow unknown claims explicitly.
4. validate date order and required fields.
5. submit via adapter.
6. show created event detail.

### API and context

- BE-API-004/005/007; `Claim` shape in API.

### Acceptance criteria

- organizer can create a seeded-style Jakarta event.
- all claims and timestamp appear on detail.
- invalid dates and API errors do not discard input.
- attendee cannot see organizer action.

### Definition of done

- global DoD and contract-shaped fixtures.

### Verification

- form happy/error paths, keyboard input, fixture 201/422, later INT-003 real API creation and reload.

## FE-004 — Accessibility request and commitment UI

- **Branch:** `feature/FE-004-requests`
- **Owner:** FE developer or AI agent
- **Priority:** P0
- **Dependencies:** FE-001 (HARD); BE-004 (SOFT)
- **Can run in parallel:** Yes, with BE-004 and other FE page tasks

**Goal:** make attendee/organizer exchange and commitment visible.

### Scope

- attendee request modal/page with arrival and note.
- attendee history.
- organizer inbox.
- response form.
- attendee accept/decline.
- status and written commitment.

### Outside scope

- messaging, notifications, calendar sync, server-side authorization logic.

### Implementation steps

1. implement submit and list views.
2. separate role actions.
3. render pending/responded/confirmed/closed/verified.
4. connect response and confirmation.
5. preserve note on failed submit.

### API and context

- BE-API-005, 008–011.

### Acceptance criteria

- fixture flow shows pending → responded → confirmed, and cannot-fulfill → closed.
- written response appears to both roles.
- invalid/duplicate actions show server error.

### Definition of done

- global DoD, no private note leaked into public event card.

### Verification

- two-role fixture journey.
- 403/409/error states.
- keyboard and screen-reader labels.
- later INT-003 real API smoke.

## FE-005 — Verification and organizer reliability UI

- **Branch:** `feature/FE-005-verification`
- **Owner:** FE developer or AI agent
- **Priority:** P0
- **Dependencies:** FE-001 (HARD); BE-005 (SOFT)
- **Can run in parallel:** Yes, with BE-005 and other FE page tasks

**Goal:** close the feedback loop after an event.

### Scope

- post-event seven-attribute verification form, submitted state, organizer profile reliability score/sample count, no-data state.

### Outside scope

- statistical claims beyond the API, ML, public review feed.

### Implementation steps

1. offer verification only for confirmed past event.
2. collect all seven statuses.
3. submit once.
4. refresh organizer profile and request status.
5. render score with sample count and `demo` context.

### API and context

- BE-API-009, 012–013.

### Acceptance criteria

- fixture past confirmed request can be verified once.
- upcoming event and repeat submit are blocked or explain server 409.
- score and sample count refresh.
- no-history shows `Belum ada verifikasi`, not 0%.

### Definition of done

- global DoD, clear non-certification wording.

### Verification

- fixture 201/409/401 cases, keyboard path, later INT-004 real API end-to-end smoke.

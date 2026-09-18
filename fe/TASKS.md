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

## FE-006 — Real register/login UI

- **Branch:** `feature/FE-006-real-auth`
- **Owner:** FE developer or AI agent
- **Priority:** P1
- **Dependencies:** FE-001 (HARD); BE-006 (SOFT)
- **Can run in parallel:** Yes, with BE-006 and other FE page tasks

**Goal:** let real users sign up/log in alongside the demo entry.

### Scope

- register form (email/password/name/role).
- login form.
- inline error display.
- demo entry buttons remain unchanged.

### Outside scope

- OAuth buttons, password reset UI, backend validation logic.

### Implementation steps

1. add register/login routes and forms.
2. call BE-API-014/015 via the existing API adapter.
3. reuse session/token storage from FE-001.
4. surface duplicate-email/invalid-credential errors inline.
5. mirror the contract in the fixture adapter until BE is ready.

### API and context

- BE-API-014, BE-API-015.

### Acceptance criteria

- a new account can register, then log in, and reach role-appropriate navigation.
- a wrong password shows an inline error.
- the demo entry still works unchanged.

### Definition of done

- global DoD, password field masked and never logged to console.

### Verification

- fixture success/error paths.
- keyboard navigation on the new forms.
- later real API smoke.

## FE-007 — Venue map rendering

- **Branch:** `feature/FE-007-venue-map`
- **Owner:** FE developer or AI agent
- **Priority:** P2
- **Dependencies:** FE-001 (HARD); BE-008 (SOFT)
- **Can run in parallel:** Yes, with BE-008 and other FE page tasks

**Goal:** show venue location on a map when coordinates are available.

### Scope

- map component on event detail rendering a single pin when `venue.lat`/`lng` are present.
- fallback to the existing address text when coordinates are absent.

### Outside scope

- routing/directions, multi-venue map, live navigation, geolocation of the user.

### Implementation steps

1. pick a lightweight map library compatible with the existing stack.
2. render a pin from `venue.lat`/`lng` on event detail.
3. fall back to plain address text when coordinates are `null`.
4. skip the map component entirely when BE never returns coordinates, so nothing looks broken.

### API and context

- BE-API-005 venue coordinate extension.

### Acceptance criteria

- an event with coordinates shows a pin at the correct location.
- an event without coordinates shows address text with no broken map.
- no console errors when coordinates are absent.

### Definition of done

- global DoD, no coordinate ever fabricated on the FE side.

### Verification

- fixtures with and without coordinates.
- manual check of pin placement for a known Jakarta address.

## FE-008 — Claim evidence media UI

- **Branch:** `feature/FE-008-media-upload`
- **Owner:** FE developer or AI agent
- **Priority:** P2
- **Dependencies:** FE-003 (HARD); BE-009 (SOFT)
- **Can run in parallel:** Yes, with BE-009 and other FE page tasks

**Goal:** let organizers attach and attendees view claim evidence photos.

### Scope

- multi-image upload control on the organizer event form.
- thumbnail gallery on event detail.
- upload progress/error state.

### Outside scope

- image editing/cropping, video, moderation UI.

### Implementation steps

1. add a multi-file input to the organizer event form.
2. call BE-API-017 per file with progress feedback.
3. render the returned `media` array as a gallery on event detail.
4. show a rejected upload's reason without losing the other selected files.

### API and context

- BE-API-017; BE-API-005 `media` array.

### Acceptance criteria

- an organizer uploads one or more images and sees them on the saved event.
- an attendee sees the same gallery on event detail.
- a rejected upload shows its reason.

### Definition of done

- global DoD, the gallery never implies certification of accuracy.

### Verification

- fixture upload success/error cases.
- gallery renders the expected number of images.
- keyboard access and alt text on gallery items.

## FE-009 — Advanced search/filter UI

- **Branch:** `feature/FE-009-search-refinements`
- **Owner:** FE developer or AI agent
- **Priority:** P2
- **Dependencies:** FE-002 (HARD); BE-010 (SOFT)
- **Can run in parallel:** Yes, with BE-010 and other FE page tasks

**Goal:** let attendees narrow event discovery beyond basic text/status.

### Scope

- filter controls for specific accessibility attributes.
- date range picker.
- sort selector (date vs. match score).

### Outside scope

- saved searches, search-based notifications, FE-side score calculation.

### Implementation steps

1. add filter/sort controls to the event list page.
2. map UI state to BE-API-018 query params.
3. call the adapter and render filtered/sorted results.
4. show an empty state distinct from loading/error.

### API and context

- BE-API-018.

### Acceptance criteria

- applying a required-attribute filter removes non-matching fixture events.
- sorting by match score reorders the list.
- an invalid combination surfaces the server's 422 message.

### Definition of done

- global DoD, filters never silently drop results without explanation.

### Verification

- fixture filter/sort combinations.
- keyboard operation of the controls.
- later real API smoke.

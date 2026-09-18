# EventEase — FE/BE API contract (proposal v0.1)

This is the **shared wire source of truth** for the hackathon. All paths are relative to `API_BASE_URL`; JSON uses `Content-Type: application/json` and responses use UTF-8. `CONFIRMED` product concepts come from the exsum; exact endpoints, auth, IDs, and payloads are **ASSUMPTIONS proposed for parallel implementation**. Freeze them in INT-001 before FE/BE integration. No existing API or repository was provided.

## Global conventions

- IDs are opaque strings. Times are ISO 8601 with timezone (example `2026-09-18T09:00:00+07:00`). Scores are integer 0–100 or `null` when there is no evidence. Coordinates are optional and are not needed for P0.
- All endpoints except login require `Authorization: Bearer <demo_token>`; BE checks `attendee` or `organizer` role and object ownership. The latest user decision uses a signed JWT for this bearer token. Demo users are seeded server-side; this endpoint does not use passwords. Real-user auth is `NEEDS DECISION`.
- All errors use `{"error":{"code":"VALIDATION_ERROR","message":"...","details":{}}}`. `details` may be `{}`. Common statuses: 400 malformed JSON, 401 missing/invalid token, 403 wrong role or owner, 404 unknown resource, 409 invalid transition/duplicate, 422 semantic validation, 500 unexpected failure. Never return stack traces or sensitive notes in errors.
- `null` means an organizer claim is unknown. Six facility claims accept exactly `0`, `0.5`, `1`, or `null`. `walking_distance_m` is a nonnegative integer or `null`. Need-profile booleans express required/not required, not medical conditions.
- Status enums: event `upcoming|completed`; request `pending|responded|confirmed|closed|verified`; response `can_fulfill|partially_fulfill|cannot_fulfill`; verification value `fulfilled|partially_fulfilled|not_fulfilled`.
- `GET` returns 200; successful creation returns 201; update/action returns 200. No endpoint returns 204. Optional query values are explicitly listed; other query parameters are ignored only if framework defaults require it, otherwise 422.

## Shared JSON shapes

`NeedProfile`:
```json
{
  "step_free_entrance": true,
  "elevator_or_ramp": true,
  "accessible_restroom": true,
  "accessible_seating": false,
  "rest_area": false,
  "parking_or_dropoff": true,
  "walking_distance": "short"
}
```
All seven keys are required on PUT. `walking_distance` is `short|moderate|any`.

`Claim`:
```json
{
  "step_free_entrance": 1,
  "elevator_or_ramp": 0.5,
  "accessible_restroom": null,
  "accessible_seating": 1,
  "rest_area": 1,
  "parking_or_dropoff": 0,
  "walking_distance_m": 180,
  "source": "organizer",
  "checked_at": "2026-09-17T10:00:00+07:00"
}
```
Six value keys and `walking_distance_m` are required, even when null. `source` is `organizer|open_data|demo`; `checked_at` is the time the claim was entered or imported, not an independent audit timestamp. If an imported value is not mapped with confidence, use null.

## Endpoints

### BE-API-001 — Demo login

**Method/path/purpose:**

`POST /api/auth/demo-login` — start one seeded attendee/organizer demo session

**Auth/headers:**

Public; `Content-Type: application/json`

**Path/query/body:**

No path or query; body

```json
{
  "account": "attendee"
}
```

or `{"account":"organizer"}`

**200 response:**

```json
{
  "token": "demo-token",
  "user": {
    "id": "u-att-1",
    "name": "Demo Attendee",
    "role": "attendee"
  }
}
```

**Errors:**

422 invalid account; common 400/500 envelope

**Implementation:**

Token must identify seeded user and role server-side; FE stores only for active demo session. No public signup in P0.

Example: `POST /api/auth/demo-login` with `{"account":"attendee"}` → the 200 JSON above.

### BE-API-002 — Read my needs

**Method/path/purpose:**

`GET /api/me/needs` — attendee's saved functional requirements

**Auth/headers:**

Attendee bearer; no request body; `Accept: application/json`

**Path/query:**

None

**200 response:**

```json
{
  "profile": {
    "step_free_entrance": true,
    "elevator_or_ramp": true,
    "accessible_restroom": true,
    "accessible_seating": false,
    "rest_area": false,
    "parking_or_dropoff": true,
    "walking_distance": "short"
  },
  "updated_at": "2026-09-17T10:00:00+07:00"
}
```

If no profile exists: `profile:null,updated_at:null` before first save.

**Errors:**

401/403/500 envelope

**Implementation:**

Do not infer a medical diagnosis from this profile.

Example: `GET /api/me/needs` with attendee bearer → the 200 JSON above.

### BE-API-003 — Save my needs

**Method/path/purpose:**

`PUT /api/me/needs` — replace all seven profile values

**Auth/headers:**

Attendee bearer; `Content-Type: application/json`

**Path/query/body:**

No path/query; body is the complete `NeedProfile` shape above

**200 response:**

Same response shape as BE-API-002 with updated timestamp

**Errors:**

401/403/422/500 envelope

**Implementation:**

Replace atomically; only the current attendee's profile.

Example: `PUT /api/me/needs` with the `NeedProfile` JSON above returns the BE-API-002 response shape and a new `updated_at`.

### BE-API-004 — List events

**Method/path/purpose:**

`GET /api/events` — event discovery and organizer's event list

**Auth/headers:**

Either role bearer; `Accept: application/json`

**Path/query/body:**

No path/body; optional `status=upcoming|completed`, `q` text (max 100 chars), `limit` 1–50 (default 20), `offset` ≥0 (default 0); organizer may pass `mine=true`

**200 response:**

```json
{
  "items": [
    {
      "id": "evt-1",
      "title": "Festival Inklusif Jakarta",
      "starts_at": "2026-09-18T09:00:00+07:00",
      "status": "upcoming",
      "venue": {
        "id": "v-1",
        "name": "Venue Demo Jakarta",
        "city": "Jakarta"
      },
      "organizer": {
        "id": "org-1",
        "name": "Demo Organizer",
        "reliability_score": 75,
        "sample_count": 2
      }
    }
  ],
  "total": 1,
  "limit": 20,
  "offset": 0
}
```

**Errors:**

401/422/500 envelope; 403 if attendee uses `mine=true`

**Implementation:**

Sort by start time, then ID. Scores require BE-API-006; FE may call it for visible cards.

Example: `GET /api/events?status=upcoming&limit=20&offset=0` → the 200 JSON above.

### BE-API-005 — Event details

**Method/path/purpose:**

`GET /api/events/{event_id}` — inspect event and all seven organizer claims

**Auth/headers:**

Either role bearer; `Accept: application/json`

**Path/query/body:**

`event_id` string; no query/body

**200 response:**

```json
{
  "id": "evt-1",
  "title": "Festival Inklusif Jakarta",
  "description": "Demo event",
  "starts_at": "2026-09-18T09:00:00+07:00",
  "ends_at": "2026-09-18T17:00:00+07:00",
  "status": "upcoming",
  "venue": {
    "id": "v-1",
    "name": "Venue Demo Jakarta",
    "city": "Jakarta",
    "address": "Jakarta, Indonesia"
  },
  "organizer": {
    "id": "org-1",
    "name": "Demo Organizer",
    "reliability_score": 75,
    "sample_count": 2
  },
  "claim": {
    "step_free_entrance": 1,
    "elevator_or_ramp": 0.5,
    "accessible_restroom": null,
    "accessible_seating": 1,
    "rest_area": 1,
    "parking_or_dropoff": 0,
    "walking_distance_m": 180,
    "source": "demo",
    "checked_at": "2026-09-17T10:00:00+07:00"
  }
}
```

**Errors:**

401/404/500 envelope

**Implementation:**

Show claim source/date. `reliability_score:null` if zero samples.

Example: `GET /api/events/evt-1` → the 200 JSON above.

### BE-API-006 — Personalized event match

**Method/path/purpose:**

`GET /api/events/{event_id}/match` — score saved attendee needs against event claims

**Auth/headers:**

Attendee bearer; `Accept: application/json`

**Path/query/body:**

`event_id` string; no query/body

**200 response:**

```json
{
  "event_id": "evt-1",
  "score": 52,
  "weight_version": "provisional-v1",
  "breakdown": [
    {
      "attribute": "step_free_entrance",
      "required": true,
      "weight": 2,
      "fulfillment": 1,
      "label": "fulfilled"
    },
    {
      "attribute": "elevator_or_ramp",
      "required": true,
      "weight": 2,
      "fulfillment": 0.5,
      "label": "partially_fulfilled"
    },
    {
      "attribute": "accessible_restroom",
      "required": true,
      "weight": 2,
      "fulfillment": null,
      "label": "unknown"
    },
    {
      "attribute": "accessible_seating",
      "required": false,
      "weight": 0.25,
      "fulfillment": 1,
      "label": "fulfilled"
    },
    {
      "attribute": "rest_area",
      "required": false,
      "weight": 0.25,
      "fulfillment": 1,
      "label": "fulfilled"
    },
    {
      "attribute": "parking_or_dropoff",
      "required": true,
      "weight": 2,
      "fulfillment": 0,
      "label": "not_fulfilled"
    },
    {
      "attribute": "walking_distance",
      "required": true,
      "weight": 2,
      "fulfillment": 1,
      "label": "fulfilled"
    }
  ],
  "unknown_attributes": [
    "accessible_restroom"
  ],
  "summary": "Some required information is unknown; contact the organizer."
}
```

**Errors:**

401/403/404 event, 409 `NEED_PROFILE_MISSING`, 500 envelope

**Implementation:**

Formula and unknown policy in `ARCHITECTURE.md`; score is server-calculated. `summary` is explanatory, not a guarantee.

Example: `GET /api/events/evt-1/match` for the sample need profile and event claim above → the 200 JSON above. `weight` is the relative coefficient; the seven weights sum to 10.5, so the example score is `round(100 × 5.5 / 10.5) = 52`.

### BE-API-007 — Publish organizer event

**Method/path/purpose:**

`POST /api/events` — create one organizer-owned event and claim

**Auth/headers:**

Organizer bearer; `Content-Type: application/json`

**Path/query/body:**

No path/query;

```json
{
  "title": "Festival Inklusif Jakarta",
  "description": "Demo event",
  "starts_at": "2026-09-18T09:00:00+07:00",
  "ends_at": "2026-09-18T17:00:00+07:00",
  "venue": {
    "name": "Venue Demo Jakarta",
    "city": "Jakarta",
    "address": "Jakarta, Indonesia"
  },
  "claim": {
    "step_free_entrance": 1,
    "elevator_or_ramp": 0.5,
    "accessible_restroom": null,
    "accessible_seating": 1,
    "rest_area": 1,
    "parking_or_dropoff": 0,
    "walking_distance_m": 180,
    "source": "organizer",
    "checked_at": "2026-09-17T10:00:00+07:00"
  }
}
```

**201 response:**

Same event shape as BE-API-005, with generated IDs and organizer set from token

**Errors:**

401/403/422 invalid dates/claim/outside supported city, 500 envelope

**Implementation:**

BE sets owner and status from timestamps; never trust a body-supplied organizer ID. `source` must be `organizer` for this endpoint; BE may set checked time itself.

Example: `POST /api/events` with body above → 201 body shaped exactly as BE-API-005.

### BE-API-008 — Submit accessibility request

**Method/path/purpose:**

`POST /api/events/{event_id}/requests` — attendee asks organizer for specific support

**Auth/headers:**

Attendee bearer; `Content-Type: application/json`

**Path/query/body:**

`event_id` string; no query;

```json
{
  "arrival_estimate": "2026-09-18T08:30:00+07:00",
  "note": "Please confirm the step-free entrance and drop-off."
}
```

**201 response:**

```json
{
  "id": "req-1",
  "event_id": "evt-1",
  "attendee_id": "u-att-1",
  "status": "pending",
  "needs_snapshot": {
    "step_free_entrance": true,
    "elevator_or_ramp": true,
    "accessible_restroom": true,
    "accessible_seating": false,
    "rest_area": false,
    "parking_or_dropoff": true,
    "walking_distance": "short"
  },
  "arrival_estimate": "2026-09-18T08:30:00+07:00",
  "note": "Please confirm the step-free entrance and drop-off.",
  "response": null,
  "created_at": "2026-09-17T10:00:00+07:00"
}
```

**Errors:**

401/403/404, 409 `EVENT_NOT_UPCOMING` or `NEED_PROFILE_MISSING`, 422 note >500 chars or invalid arrival, 500 envelope

**Implementation:**

Snapshot needs at creation. Attendee may submit multiple requests only if they concern materially different needs; for P0, one active request per attendee/event, else 409 `ACTIVE_REQUEST_EXISTS`.

Example: `POST /api/events/evt-1/requests` with body above → the 201 JSON above.

### BE-API-009 — List my requests

**Method/path/purpose:**

`GET /api/requests` — role-scoped attendee history or organizer inbox

**Auth/headers:**

Either role bearer; `Accept: application/json`

**Path/query/body:**

No path/body; optional `status=pending|responded|confirmed|closed|verified`, `limit` 1–50 default 20, `offset` ≥0 default 0

**200 response:**

```json
{
  "items": [
    {
      "id": "req-1",
      "event_id": "evt-1",
      "event_title": "Festival Inklusif Jakarta",
      "status": "pending",
      "arrival_estimate": "2026-09-18T08:30:00+07:00",
      "note": "Please confirm the step-free entrance and drop-off.",
      "needs_snapshot": {
        "step_free_entrance": true,
        "elevator_or_ramp": true,
        "accessible_restroom": true,
        "accessible_seating": false,
        "rest_area": false,
        "parking_or_dropoff": true,
        "walking_distance": "short"
      },
      "response": null,
      "created_at": "2026-09-17T10:00:00+07:00"
    }
  ],
  "total": 1,
  "limit": 20,
  "offset": 0
}
```

**Errors:**

401/422/500 envelope

**Implementation:**

Return only own attendee requests or requests for own organizer events. Include full response when present.

Example: `GET /api/requests?status=pending` → the 200 JSON above.

### BE-API-010 — Organizer response

**Method/path/purpose:**

`POST /api/requests/{request_id}/response` — document organizer capability

**Auth/headers:**

Owning organizer bearer; `Content-Type: application/json`

**Path/query/body:**

`request_id` string; no query;

```json
{
  "decision": "partially_fulfill",
  "note": "Step-free entrance available; restroom assistance needed."
}
```

**200 response:**

```json
{
  "id": "req-1",
  "status": "responded",
  "response": {
    "decision": "partially_fulfill",
    "note": "Step-free entrance available; restroom assistance needed.",
    "responded_at": "2026-09-17T11:00:00+07:00"
  }
}
```

; for `cannot_fulfill`, status is `closed`

**Errors:**

401/403/404, 409 `INVALID_REQUEST_STATE`, 422 invalid enum/blank note, 500 envelope

**Implementation:**

Response is immutable after submission in P0. Existing request snapshot stays unchanged.

Example: `POST /api/requests/req-1/response` with body above → the 200 JSON above.

### BE-API-011 — Attendee confirms response

**Method/path/purpose:**

`POST /api/requests/{request_id}/confirm` — accept organizer's written commitment

**Auth/headers:**

Owning attendee bearer; `Content-Type: application/json`

**Path/query/body:**

`request_id` string; no query;

```json
{
  "accepted": true
}
```

; false closes request

**200 response:**

```json
{
  "id": "req-1",
  "status": "confirmed",
  "confirmed_at": "2026-09-17T11:10:00+07:00",
  "response": {
    "decision": "partially_fulfill",
    "note": "Step-free entrance available; restroom assistance needed.",
    "responded_at": "2026-09-17T11:00:00+07:00"
  }
}
```

; false yields `closed`, `confirmed_at:null`

**Errors:**

401/403/404, 409 `INVALID_REQUEST_STATE` or `CANNOT_FULFILL`, 422/500 envelope

**Implementation:**

Only `responded` with can/partial can become confirmed. `cannot_fulfill` is already closed.

Example: `POST /api/requests/req-1/confirm` with `{"accepted":true}` → the 200 JSON above.

### BE-API-012 — Post-event verification

**Method/path/purpose:**

`POST /api/requests/{request_id}/verification` — compare commitment with actual experience

**Auth/headers:**

Owning attendee bearer; `Content-Type: application/json`

**Path/query/body:**

`request_id` string; no query;

```json
{
  "attributes": {
    "step_free_entrance": "fulfilled",
    "elevator_or_ramp": "partially_fulfilled",
    "accessible_restroom": "not_fulfilled",
    "accessible_seating": "fulfilled",
    "rest_area": "fulfilled",
    "parking_or_dropoff": "partially_fulfilled",
    "walking_distance": "fulfilled"
  }
}
```

; all seven keys required

**201 response:**

```json
{
  "id": "ver-1",
  "request_id": "req-1",
  "status": "verified",
  "submitted_at": "2026-09-19T10:00:00+07:00",
  "organizer_reliability": {
    "score": 71,
    "sample_count": 3
  }
}
```

**Errors:**

401/403/404, 409 `EVENT_NOT_COMPLETED`, `INVALID_REQUEST_STATE`, or `ALREADY_VERIFIED`, 422 missing/invalid attribute, 500 envelope

**Implementation:**

Only confirmed requests after event end; unique verification per request; BE recomputes organizer score. Demo past event must have confirmed seeded request.

Example: `POST /api/requests/req-1/verification` with body above → the 201 JSON above.

### BE-API-013 — Organizer reliability profile

**Method/path/purpose:**

`GET /api/organizers/{organizer_id}` — public organizer identity and feedback indicator

**Auth/headers:**

Either role bearer; `Accept: application/json`

**Path/query/body:**

`organizer_id` string; no query/body

**200 response:**

```json
{
  "id": "org-1",
  "name": "Demo Organizer",
  "reliability": {
    "score": 71,
    "sample_count": 3,
    "window_size": 20,
    "updated_at": "2026-09-19T10:00:00+07:00"
  }
}
```

**Errors:**

401/404/500 envelope

**Implementation:**

With no verification: `score:null,sample_count:0,updated_at:null`; display count and demo provenance; no implied certification.

Example: `GET /api/organizers/org-1` → the 200 JSON above.

## Contract verification

BE publishes fixtures for success and listed errors. FE can use the same fixtures before BE is ready. Before INT-002, test JSON key/enums against both implementations; test auth owner checks and lifecycle conflicts. Any breaking edit updates this file first, increments version, and is agreed by both owners. Match scores must be computed from the versioned weight configuration. The example match score above is exact for its sample profile and claim; reliability examples assume earlier seeded verifications and are illustrative.

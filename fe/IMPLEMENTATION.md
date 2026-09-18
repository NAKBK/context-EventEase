# EventEase — Frontend execution plan

Repository: **frontend GitHub repository, URL `NEEDS DECISION`**. The exsum architecture diagram specifies **Next.js + Tailwind CSS**; versions, package manager, route structure, and existing conventions depend on the actual repository. This document covers FE only. Read `../shared/PROJECT.md`, `../shared/ARCHITECTURE.md`, and `../shared/API.md` first.

## Strategy

Use one typed API adapter and contract-shaped fixture set. A configuration switch selects fixture or real API; the page components use the same adapter interface. Keep route and component boundaries small: attendee need/profile, event list/detail and match, request/history/verification; organizer dashboard, event publishing, request inbox, profile/reliability. Use semantic form controls, visible labels, keyboard focus, and non-colour-only match/status indicators because accessibility is central to the product.

FE does not compute the authoritative score or reliability indicator. A fixture can contain precomputed values matching `API.md`. Treat `null` claims as unknown and show claim provenance/date. Retain form input after failed requests. The low-fidelity exsum screens are design reference, not fixed layout specifications.

## Order and dependencies

1. **FE-001:** app shell, demo role entry, API adapter, fixtures. Requires contract freeze only as SOFT; may scaffold before it.
2. **FE-002:** attendee need form, event discovery/detail, score breakdown. HARD on FE-001; SOFT on BE-002/003 because fixtures allow independent work.
3. **FE-003:** organizer event publishing and claims form. HARD on FE-001; SOFT on BE-002.
4. **FE-004:** request submit/history, organizer response, attendee confirmation. HARD on FE-001; SOFT on BE-004.
5. **FE-005:** post-event verification and organizer reliability display. HARD on FE-001; SOFT on BE-005.
6. Join INT-002/003/004 when respective BE routes and FE views pass independently.

FE-002–005 can be distributed among developers/agents in parallel after FE-001, with separate branches and minimal shared-file edits. Reserve T+14–18h for integration and T+21–24h for QA/rehearsal per `../shared/IMPLEMENTATION.md`.

## FE verification and fallback

For each form, manually or automatically verify normal, empty, validation, loading, HTTP error, and keyboard flow. Verify two profiles yield visibly different match breakdowns using fixtures. Verify a request does not show `confirmed` before attendee acceptance and that verification is unavailable for an upcoming event. If the API is delayed, retain fixture mode for page review and spend integration time on adapter/contract checks. P1 maps and spatial directions are cut first.

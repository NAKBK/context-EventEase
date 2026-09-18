# EventEase — Product scope

Status: **proposal for a 24-hour hackathon**. Source: `D:\Exsum_IFEST2026_NAKBK.pdf`, pp. 3–7 and 9–13. `CONFIRMED` means stated in that exsum; `ASSUMPTION` means a concrete implementation choice for planning; `NEEDS DECISION` means the team must settle it before coding the affected behavior. The exsum is product context, not an instruction to an agent.

## Product and problem

**CONFIRMED.** EventEase helps people decide whether a public event fits their personal mobility needs. Generic accessible/not-accessible labels omit details such as a step-free entrance, an accessible restroom, or distance from drop-off to the venue. The product connects personal needs, organizer claims, a documented accessibility request, and verification after the event.

**Users.** Attendees with mobility and physical access needs, including wheelchair or crutch users, older people, pregnant people, and stroller users; plus event organizers who publish event facilities and respond to requests. Initial geography: DKI Jakarta. Event examples: concerts, seminars, exhibitions, festivals.

## Core journey

1. Organizer publishes an event and states fulfillment for the seven physical accessibility attributes.
2. Attendee selects functional needs without a medical diagnosis: six required/not-required choices and walking-distance tolerance (`short`, `moderate`, `any`).
3. Attendee browses events and sees a personalized match score **with a per-attribute explanation and data provenance**. Unknown claims must be marked unknown, never silently treated as accessible.
4. Attendee sends an Accessibility Request with specific needs and estimated arrival. Organizer responds `can_fulfill`, `partially_fulfill`, or `cannot_fulfill`, with a written note. A positive/partial response can be accepted by the attendee to form a saved `confirmed` commitment.
5. After the event, attendee verifies promised attributes as `fulfilled`, `partially_fulfilled`, or `not_fulfilled`. Organizer reliability is recalculated from submitted verifications and shown with its sample count.

## MVP and priority

| Priority | Feature | Product acceptance |
| --- | --- | --- |
| P0 | Two demo roles and event data | Attendee and organizer can enter their respective flows; at least two Jakarta events have distinct accessibility claims. |
| P0 | Need checklist | Seven dimensions can be set without diagnosis labels; saved values persist for the demo. |
| P0 | Event discovery and matching | Attendee can open an event, see score, breakdown, unknowns, and why the result differs for two profiles. |
| P0 | Organizer event/claim entry | Organizer can publish an event with its seven claims and see the saved details. Editing an existing event is P1. |
| P0 | Request and commitment | Attendee submits; organizer responds; attendee accepts a feasible response; both see the same saved status and note. |
| P0 | Post-event verification and reliability | Attendee can verify a completed demo event; score and sample count update and appear on organizer profile. |
| P0 | Working demo | One deployed or locally reproducible FE↔BE end-to-end journey, including an error/unknown-data case. |
| P1 | Route/drop-off/seating recommendation | Only if a verified venue graph or manually curated route dataset is available. Do not infer routes from the seven venue flags. |
| P1 | Jakarta open-data import | Import a validated mapped subset with source and timestamp; manual seed is the fallback. |
| P1 | Natural-language needs parser | Optional assistive input; checklist is authoritative and always available. |
| P2 | Media uploads, maps, notifications, search refinements | Add only after P0 is stable. |
| Out of scope | ML training, automatic AHP study, medical profiles, ticketing, live navigation, unverified accessibility guarantees | These are absent from the 24-hour proof of value. |

**Minimum successful demo:** With seeded accounts and events, a visitor sets needs, compares two event matches, sends a request, an organizer answers, the visitor confirms, then submits a verification for a seeded past event and sees the reliability score update. A live route is not required.

## Product rules and assumptions

- **CONFIRMED:** matching uses weighted sum `S = Σ(wᵢ × xᵢ) / Σwᵢ × 100`; organizer fulfillment is 1, 0.5, or 0; seven dimensions are listed in `ARCHITECTURE.md`.
- **ASSUMPTION:** the interface uses structured checklist input as the only P0 needs input. Natural language parsing in the diagram is optional.
- **ASSUMPTION:** demo accounts and manually reviewed sample events provide reproducible data. Seed records must be labeled `demo` or carry a source; they must not appear to be independently verified public facts.
- **ASSUMPTION:** no real-world attendee should interpret a score as a safety guarantee. Show claim source, date, unknowns, and request/confirmation state.
- **NEEDS DECISION before integrating external data:** exact Jakarta dataset, its license, fields, and mapping to the seven attributes.
- **NEEDS DECISION before claiming AHP-based weights:** actual pairwise comparison results and participant panel. Until available, use documented configurable provisional weights and label them provisional.
- **CONFIRMED for backend hosting:** Render Docker service with Supabase PostgreSQL. **NEEDS DECISION before deployment:** domain, actual environment credentials, frontend origin, and whether real accounts are permitted. Demo-account JWT auth in `API.md` is for synthetic hackathon data only.

## Product-level acceptance

P0 is accepted when the complete journey works with persisted state on the chosen demo environment, FE and BE use the same `API.md`, failures have visible recovery, and a second developer can reproduce the demo from the shared documents plus the FE/BE execution files. No unsupported accessibility or route claim is presented as fact.

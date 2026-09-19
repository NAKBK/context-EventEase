<div align="center">

# EventEase - Context Hub

<p align="center">
  Team NAKBK, IFEST 2026 Hackathon. EventEase helps people with mobility access needs decide whether a public event fits them, and keeps organizers accountable for what they promise.
</p>

</div>

## What This Repository Is

EventEase is split across three repositories. This one holds the project planning documents and the map to everything else. The code and its own technical documents live in the other two.

| Repository | Contents |
| --- | --- |
| [context-EventEase](https://github.com/NAKBK/context-EventEase) (this repo) | Proposal summary, product scope, API contract, task plans, and this guide |
| [FE-EventEase](https://github.com/NAKBK/FE-EventEase) | Frontend web application (Next.js) |
| [BE-EventEase](https://github.com/NAKBK/BE-EventEase) | Backend API service (FastAPI, PostgreSQL) |

## Start Here

Where to find what a reviewer is most likely to look for.

| I want to... | Go to |
| --- | --- |
| Run the project | The README of each code repository: [FE README](https://github.com/NAKBK/FE-EventEase#readme) and [BE README](https://github.com/NAKBK/BE-EventEase#readme). Each lists prerequisites and build/run steps. Start the backend first, then the frontend |
| See what changed from the proposal, and why | [FE `docs/PERUBAHAN.md`](https://github.com/NAKBK/FE-EventEase/blob/main/docs/PERUBAHAN.md) |
| Understand the system design | [Architecture](#architecture) below |
| Understand why a technical decision was made | [Decision records](#decision-records-adr) below |
| Check the API between frontend and backend | [`shared/API.md`](shared/API.md) for the contract, and [BE `docs/api-status.md`](https://github.com/NAKBK/BE-EventEase/blob/main/docs/api-status.md) for what is implemented |
| Walk through the full product journey | [BE `docs/flows.md`](https://github.com/NAKBK/BE-EventEase/blob/main/docs/flows.md) |
| Read the original proposal | [EXSUM.md](EXSUM.md) (in Indonesian) |

## Changes From the Proposal

[PERUBAHAN.md](https://github.com/NAKBK/FE-EventEase/blob/main/docs/PERUBAHAN.md) lives in the frontend repository under `docs/`. Each entry records the condition in the proposal, what was changed, why, and the impact on the core problem. It covers:

1. Provisional match weights instead of an AHP panel study
2. No natural-language needs parser
3. No route, drop-off, or seating recommendation
4. Venue data from organizers and demo data instead of open data
5. Event photos instead of supporting documents

Some of these depend on backend decisions, which are explained in the decision records below.

## Architecture

| Document | What it covers |
| --- | --- |
| [`shared/ARCHITECTURE.md`](shared/ARCHITECTURE.md) | The planned system boundary: components, data, and how the frontend and backend fit together |
| [BE `docs/architecture.md`](https://github.com/NAKBK/BE-EventEase/blob/main/docs/architecture.md) | How the backend is actually built: modules, layers, and data flow |
| [`shared/PROJECT.md`](shared/PROJECT.md) | Product scope, the core journey, and what was in or out of the MVP |

## Decision Records (ADR)

Each record explains one backend decision: the context, the choice, and the consequences. They are in [BE `docs/adr/`](https://github.com/NAKBK/BE-EventEase/tree/main/docs/adr).

| Record | Decision |
| --- | --- |
| [ADR-0001](https://github.com/NAKBK/BE-EventEase/blob/main/docs/adr/0001-fastapi-modular-monolith.md) | A single FastAPI backend organized into modules, not microservices |
| [ADR-0002](https://github.com/NAKBK/BE-EventEase/blob/main/docs/adr/0002-dual-auth-demo-and-real.md) | Demo login and real register/login issue the same kind of token |
| [ADR-0003](https://github.com/NAKBK/BE-EventEase/blob/main/docs/adr/0003-provisional-weighted-match-formula.md) | The match score is a documented formula with provisional weights |
| [ADR-0004](https://github.com/NAKBK/BE-EventEase/blob/main/docs/adr/0004-reliability-scoped-to-organizer.md) | The reliability score belongs to the organizer, not the venue or event |
| [ADR-0005](https://github.com/NAKBK/BE-EventEase/blob/main/docs/adr/0005-supabase-storage-for-media.md) | Event photos are stored in Supabase Storage |
| [ADR-0006](https://github.com/NAKBK/BE-EventEase/blob/main/docs/adr/0006-saw-scoring-not-ml.md) | Scoring uses a weighted-sum method, not machine learning |

## Contents of This Repository

```text
context-EventEase/
├── EXSUM.md                   # Original proposal (Indonesian)
├── shared/                    # Documents that apply to both frontend and backend
│   ├── PROJECT.md             # Product scope and core journey
│   ├── ARCHITECTURE.md        # Planned system boundary
│   ├── API.md                 # Frontend/backend API contract
│   ├── IMPLEMENTATION.md      # Overall 24-hour plan
│   ├── TASKS.md               # Cross-repository and QA tasks
│   ├── DEPENDENCY_AND_IMPLEMENTATION_PLAN.md  # Task dependencies and roadmap
│   └── AGENTS.md              # Working rules for AI coding assistants
├── fe/                        # Frontend plan and tasks
│   ├── IMPLEMENTATION.md
│   └── TASKS.md
└── be/                        # Backend plan and tasks
    ├── IMPLEMENTATION.md
    └── TASKS.md
```

## Other Documents in the Code Repositories

| Repository | Document | Purpose |
| --- | --- | --- |
| FE | [`README.md`](https://github.com/NAKBK/FE-EventEase#readme) | Prerequisites, how to build and run, a walkthrough of the full journey |
| FE | [`docs/PERUBAHAN.md`](https://github.com/NAKBK/FE-EventEase/blob/main/docs/PERUBAHAN.md) | Changes from the proposal |
| BE | [`README.md`](https://github.com/NAKBK/BE-EventEase#readme) | Prerequisites, how to build and run, testing |
| BE | [`docs/api-status.md`](https://github.com/NAKBK/BE-EventEase/blob/main/docs/api-status.md) | Which endpoints are done, and which are not |
| BE | [`docs/flows.md`](https://github.com/NAKBK/BE-EventEase/blob/main/docs/flows.md) | The product journey as step-by-step API calls |
| BE | [`docs/playbook.md`](https://github.com/NAKBK/BE-EventEase/blob/main/docs/playbook.md) | Running, deploying, and extending the backend |

## Note on the Planning Documents

The files in `shared/`, `fe/`, and `be/` are the plans written for the 24-hour build. Where the finished implementation differs from them or from the proposal, the code repositories are the reference: see [PERUBAHAN.md](https://github.com/NAKBK/FE-EventEase/blob/main/docs/PERUBAHAN.md) for differences from the proposal and `docs/api-status.md` in the backend for what is implemented.

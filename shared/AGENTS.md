# EventEase — AI agent working rules

Read `PROJECT.md`, `ARCHITECTURE.md`, `API.md`, the appropriate `fe/` or `be/` plan and task file, and `shared/IMPLEMENTATION.md` before starting a task. This workspace is a documentation staging area; the two GitHub repository URLs and their actual codebases have not been supplied. `fe/` and `be/` describe future separate repositories; do not assume these directories are the deployed code repositories.

## Source of truth

1. Explicit latest product decision from the team; log changes in the affected documents.
2. `shared/API.md` for FE/BE payloads, states, errors, and endpoint behavior.
3. `shared/ARCHITECTURE.md` for technical boundaries and scoring rules.
4. `shared/PROJECT.md` for product scope and P0/P1/P2.
5. `shared/IMPLEMENTATION.md` for cross-team order and cut line.
6. Scope-specific `fe/IMPLEMENTATION.md`, `be/IMPLEMENTATION.md`, and task files for execution.
7. This file for operating rules.

If documents conflict, identify the conflict and apply the higher-priority source only when the intent is clear. Flag a material product/API conflict as `NEEDS DECISION`; never silently change one side. `CONFIRMED` means present in the exsum, `ASSUMPTION` means proposed for planning, and `NEEDS DECISION` requires a team choice before depending on it.

## Work discipline

- One task ID, owner, and feature branch per code change. Use `feature/FE-XXX-description`, `feature/BE-XXX-description`, or `feature/INT-XXX-description` in the relevant Git repository. Documentation-only tasks may use `docs/` branches if that repository's convention requires them.
- Keep changes in the assigned repository and task scope. Follow existing conventions and tests after the actual repositories are provided. Avoid unrelated refactors and unnecessary dependencies.
- FE owns interaction, presentation, accessibility, and contract-shaped fixtures. BE owns validation, auth/authorization, scoring, persistence, and state transitions. Shared API changes require coordinated FE/BE review; update `shared/API.md` first.
- A HARD dependency prevents starting. A SOFT dependency allows work with contract fixtures or documented assumptions. An INTEGRATION dependency requires both sides complete before connecting them. Do not block FE UI on an unfinished BE endpoint when the contract exists.
- For every task, implement the acceptance criteria, run the listed verification, report exact commands/results, and state remaining limitations. Use manual checks where tests are not yet established. Do not add tests that merely copy implementation details.
- Handle loading, empty, validation, authorization, network, and unknown-data states. Unknown accessibility information must never be presented as verified or safe.
- Use demo data only when marked as demo; preserve data provenance and date. Treat DKI import, route graph, and LLM parsing as optional integrations with documented fallbacks.
- Keep secrets in server environment only. Do not put real personal health details, auth tokens, or private request notes in logs, screenshots, or seed data. Collect functional needs rather than medical diagnoses.

## Decide versus escalate

Choose the simplest reversible implementation detail independently when it stays within `API.md` and P0 scope; record the assumption in the relevant PR/task. Ask the product/technical owner when a choice changes the score meaning, status transitions, public accessibility claims, auth model, data privacy, external data license, or API shape. During the 24-hour hackathon, if no answer arrives, keep the affected integration behind a fixture and progress on independent work.

## Global definition of done

Task acceptance criteria pass; relevant automated or manual verification is recorded; no known blocker breaks the core journey; API and documented scope agree with the code; error/unknown states are visible; changes are isolated and ready for another developer to review and merge. Production-grade hardening is not a hackathon gate unless it is needed for the demo or basic user safety.

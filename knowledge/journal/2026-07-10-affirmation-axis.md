---
type: Journal
date: 2026-07-10
session: affirmation-axis
status: shipped
related: [[decisions/2026-06-25-public-first-standard]]
---

# Mirror FOUNDRY's basis/status affirmation split into §4 and §7

## Context
FOUNDRY (a private, non-public knowledge bundle) hit a real bug: agent-written decision records had no field distinguishing "a human ratified this" from "an agent inferred this," so unreviewed musings read as ratified policy. FOUNDRY designed and shipped a fix locally (schema + mechanical enforcement + three review surfaces, tested against 613 real records) before proposing anything upstream — see FOUNDRY's own `knowledge/specs/2026-07-09-decision-record-affirmation.md`. This session ports the schema half of that fix into the spec itself.

## What changed
- `SPEC.md` §4 frontmatter: `confidence:` was doing three jobs on one field (claim provenance, record affirmation, and a convergence confidence-gate score). Split into `basis:` (claim provenance, enum borrowed verbatim from knowledge-catalog #151/#159), `status:` (record affirmation — `proposed | accepted | superseded | deprecated`, ADR semantics, only meaningful on `type: decision`), `confidence:` (kept, now just the ordinal human-gradable band), and `score:` (new, optional 0.0–1.0, present only when actually computed).
- `SPEC.md` §7 convergence protocol: added a gate-blindness note — a confidence gate can't tell a human-affirmed record from an agent-inferred one, that's `status:`'s job. A `type: decision` at `status: proposed` may still rise per `rises:`, but must carry its `status:` visibly and render as a proposal, never as canonical.

## Decisions made
No new decision record — this is a mechanical port of a design already decided and evidenced in FOUNDRY's local build, not a fresh judgment call for this repo.

## What was tried and abandoned
Nothing abandoned this session.

## Open threads
- [ ] The §7 note references FOUNDRY's spec via a placeholder link (`github.com/inkxel/foundry` — FOUNDRY isn't public). Replace once/if FOUNDRY's spec gets a public counterpart, or drop the placeholder if it never does.
- [ ] Tier 3 (a real affirmation-rate measurement over live usage) is running in FOUNDRY now but not done — nothing to propose upstream on knowledge-catalog #151/#158/#159 yet. Revisit once that data exists.

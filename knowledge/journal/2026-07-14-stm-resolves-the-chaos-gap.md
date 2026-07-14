---
type: Journal
date: 2026-07-14
session: stm-resolves-the-chaos-gap
status: shipped
related: [[2026-07-13-temporal-record-split-and-local-field]]
---

# stm/ added to §3 — resolves "the chaos gap" as a real spec section, not just a problem statement

## Context
A parallel session on 2026-07-13 wrote up "the chaos gap" — a problem statement (not a design) arguing that every layer of a `.knowledge` bundle optimizes for precision, and that this may be structurally hostile to creative/generative use, since refinement is lossy and discards exactly the incidental detail that sparks non-generic output. It surveyed prior art (OKF has zero coverage; SOUL.md in the wild is either pure-accumulation or pure-distillation, never derived from a maintained bundle) and handed off a specific set of questions to whichever session was deep in this week's capsule builds, since several of that session's own decisions (the sources/ledger/journal voice split, the skills console-vs-capsule test, the `rises:` resolution) bore directly on it.

That handoff was answered same-day in `knowledge/stm/chaos-gap-agent-response.md` (FOUNDRY-local, not in this repo) — analysis only, no spec change, as instructed. The core finding: the problem was being treated as one question ("where does chaos live") when it's actually two — an ambient-sampling *mechanism* (console-side, needs no new bundle folder, works today against any bundle's existing `sources/`) and a *capture surface* for pre-verbal first-person material (genuinely new, genuinely small). Tucker separately, later the same day, connected the second half directly to something already built and load-bearing in his own daily-driver system (FOUNDRY's `knowledge/stm/`) rather than inventing new "chaos"/"stream" vocabulary — and caught the one thing that response's own reasoning had gotten wrong: a time-boxed/triage-after-N-days policy would silently destroy the exact material this is for (his own stated pattern — "I find these ideas months before people").

## What changed
- §3: added `stm/` to the bundle layout — **person-only**, optional, append-only, epistemically inert by construction.
- §3: new subsection explaining what it's for (pre-verbal material precision-only pipelines discard first) and five non-negotiable rules: person-only for ownership reasons (not the "interior" reasoning that governs `journal/` — that test doesn't actually apply here, importing it was the earlier session's mistake), structurally inert (no `confidence`/`basis`/`status`/`classification`, ever), `rises: never` as a structural consequence (not a policy choice — curating it enough to safely generalize is the exact operation that would destroy it), never an OKF export target (OKF's own "curated insight" self-definition excludes it), and append-only with no time-boxing (storage is cheap, forced expiry is the single most common mistake here).
- Explicitly framed `stm/` as **not a fourth voice** alongside `sources/`/`ledger/`/`journal/` — those three are distinguished by who's speaking; `stm/` is distinguished by how material is read (ambient vs. retrieved), which is why it doesn't extend that list, it sits beside it.

## Decisions made
- `stm/` is real bundle structure now, not a parked problem statement. Scope held deliberately narrow: person-type only, no schema beyond append-only-ness, no sampling-policy prescription (genuinely unresolved, left to implementations).
- The ambient-sampling *mechanism* itself (reading a handful of unranked raw fragments into context on demand) is explicitly NOT part of this spec change — it needs no bundle-format support at all, since it operates on `sources/`, which is already universal. That's a console/runtime concern, out of this repo's scope by the same test §3 already uses for `skills/`.
- Considered and rejected: giving `project`-type bundles their own `stm/` too. A project's equivalent of "sparks" (killed lines, abandoned directions) are artifacts with a home already (`sources/`); the genuinely new capture-surface need — pre-verbal, first-person, no single owner otherwise — only exists for `person` bundles.

## What was tried and abandoned
The earlier same-day response speculated `stm/`-equivalent content might reasonably live "peer to `journal/`, roughly one file" without fully committing to folder-vs-file. Settled here: a folder (matching `journal/`'s own shape), since FOUNDRY's real, load-bearing precedent is already a folder of small append-only scratch files, not one monolith — and there's no reason to diverge from working practice to satisfy a preference for fewer files.

## Open threads
- [ ] Sampling policy for how `stm/` content actually gets surfaced into context — genuinely unresolved, explicitly left to implementations rather than prescribed here (same discipline already applied to `journal/`'s cadence in §3).
- [ ] FOUNDRY's own `knowledge/stm/` currently runs a 7-day-triage-or-delete policy, which this section explicitly names as the mistake to avoid. FOUNDRY hasn't been migrated to match yet — a live inconsistency between the spec and its own origin case, tracked as FOUNDRY's problem to fix on its own migration timeline (see [[project_foundry_tucker_knowledge_unification]]), not this repo's.
- [ ] Whether an OKF issue is worth filing now that dotKnowledge has landed somewhere concrete on this (§9's existing "may be OKF's correct scope boundary, not its gap" framing still stands) — not filed yet, no rush per the github-contributing discipline of reading the room before posting.

## Related
- FOUNDRY-local: `knowledge/specs/2026-07-13-the-chaos-gap.md` (the problem statement + full analysis this resolves), `knowledge/stm/chaos-gap-agent-response.md` (the same-day analysis-only response), `workbench/outbox/chaos-gap-agent-handoff-prompt.md` (the original handoff).
- [[2026-07-13-temporal-record-split-and-local-field]] — the sources/ledger/journal split this builds directly on.

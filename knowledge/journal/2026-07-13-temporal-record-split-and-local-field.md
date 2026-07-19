---
type: Journal
date: 2026-07-13
session: temporal-record-split-and-local-field
status: shipped
related: []
---

# journal/ split into sources/ledger/journal; local: field added; filtered vs de-identified resolved

## Context
Follow-on to [[2026-07-12-capsule-scale-prep-audit]]. Tucker noticed `client-knowledge-template`'s docs still described a "journal entry format" with no `journal/` folder to hold it — leftover boilerplate from a FOUNDRY/Throughline-shaped scaffold, never adapted to that template's own deliberate no-journal decision. That symptom raised the real question: is "journal continuously" a universal `.knowledge` convention, or specifically FOUNDRY/Tucker's personal daily-habit baggage that shouldn't be assumed for every bundle.

Separately, real evidence surfaced from CoSi Platform's own already-accepted broker architecture (`cosi-platform/knowledge/decisions/2026-07-01-broker-is-thin-control-plane-capsules-are-git-repos.md`, `2026-07-10-knowledge-capsule-architecture.md`) — reviewed against two FigJam boards (v1 simple layout, v2 broker architecture) Tucker had whiteboarded with another agent. That work already uses a `local: true|false` bundle flag and a real `filtered`/`de-identified` split in practice, ahead of the spec catching up.

## What changed
- **§3 bundle layout** — replaced the single `journal/` folder with three: `sources/` (raw intake, universal), `ledger/` (agent/harness-authored append-only activity log, universal), `journal/` (human first-person voice, **`person` bundles only**). Added prose distinguishing the three voices and stating explicitly that the spec fixes the mechanism (voice, perspective, append-only-ness) but never prescribes cadence — daily journaling is an implementation's own working rhythm (FOUNDRY's), not something this format requires.
- **§5 manifest** — added `local: true | false`. Validated by (not copied from) CoSi Platform's own accepted design: `true` (default) = sovereign, cloneable, offline-capable; `false` = must stay behind a mediated runtime, streamed, never cloned raw. This is a transport/handling property, distinct from `access:`/`rises:`.
- **§6 rises:** — resolved the ambiguity flagged 2026-07-12. `filtered` (person bundles, gated by the subject's own opt-in — consent is the mechanism) vs. `de-identified` (org/brand/project bundles, gated by mechanical anonymization — no individual consent needed because the subject isn't a person). They were previously both defined circularly using "de-identified."
- **§9** — narrowed the open question to what's still actually open: the mechanical de-identification contract (the checklist/algorithm that earns the `de-identified` label), now that the vocabulary itself is resolved.

## Decisions made
- Person bundles get `journal/`; org/brand/project bundles do not — they get `ledger/` alone. This isn't a stylistic choice, it's downstream of §2's first-person/canonical perspective split, applied one level deeper into bundle layout than §2 previously reached.
- `local:` is a spec-level manifest field, not a broker-implementation-private config value — because a bundle's cloneable-vs-streaming-only handling requirement needs to be legible to *any* conforming reader (Throughline, a future non-CoSi tool), not just the one broker that happens to enforce it today.

## What was tried and abandoned
Considered naming the manifest field `distribution:` instead of `local:` for clarity (avoids "local" reading as "local-first app" or "local model"). Kept `local:` — it's already load-bearing vocabulary in CoSi Platform's own accepted decision record, and introducing a second name for the same concept between an internal accepted decision and the public spec was a cost with no real benefit.

## Open threads
- [ ] The mechanical de-identification contract (§9) is still unwritten — narrowed from the vocab question this session, not resolved.
- [ ] Whether this repo's own `knowledge/journal/` (this file's home) should itself become `knowledge/ledger/` under the new framework — dotKnowledge-the-spec-repo is a `project` bundle by its own type system, and a project's temporal record is `ledger/`, not `journal/`. Not renamed this session; flagged for whenever it's worth the churn.
- [ ] CoSi Platform's broker design (Ledger/Runtime + a separate `grants` DB table) uses "Ledger" for a *fleet-level* bundle registry — a different scope than this spec's new *per-bundle* `ledger/` folder. Same word, two different scopes, both now real. Worth a naming pass on CoSi Platform's side if it causes confusion in practice; not a dotKnowledge spec concern.

## Related
- [[2026-07-12-capsule-scale-prep-audit]] — where the `rises:` ambiguity was first flagged, unresolved.
- `cosi-platform/knowledge/decisions/2026-07-01-broker-is-thin-control-plane-capsules-are-git-repos.md` (private, CoSi-internal) — real-world validation for `local:`, read but not linked from the public spec.

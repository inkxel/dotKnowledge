---
type: Journal
date: 2026-07-13
session: skills-boundary-and-pr195-check
status: shipped
related: [[2026-07-13-temporal-record-split-and-local-field]]
---

# skills/ console-vs-bundle test resolved; checked PR #195 for impact

## Context
Tucker caught a real contradiction in earlier framing: skills/tooling was said to stay in the console (never the bundle), but `client-knowledge-template` already has its own `skills/` and §3's bundle layout already lists `skills/` as an optional bundle layer. Also asked whether GitHub PR #195 (GoogleCloudPlatform/knowledge-catalog) — new movement on the #148 typed-edges thread this whole session has been engaged in — affects any current work.

## What changed
- §3: added the actual test for which side of the console/bundle line a skill sits on — does the capability make sense independent of which subject's bundle is mounted (console-side: daily-briefing routines, session-wrap-up rituals) vs. does it only exist because of facts particular to *this* subject (bundle-resident: a brand's own voice-checker, a person's own voice-matching skill) — because it has to survive a handoff, same as a brand's actual voice guidelines would.
- §9: narrowed the "how do subject-specific skills compose with shared skills" open question — placement is resolved, what's still open is the *promotion* path (a client-specific skill proving broadly useful enough to become a shared/agency skill, without just copying the file).
- §9: updated the affirmation-event-location open question with PR #195's concrete shape — `supersedes`/`contested_by` resolution events pinned to `log.md`, keyed to stable concept ids, composing with the `proposed/accepted/superseded/deprecated` axis this spec's own `status:` already uses. Not yet merged; noted as the target to adopt once it lands, not adopted yet.

## Decisions made
- The console/bundle test for skills (independent-of-subject vs. subject-specific) — a real design decision, not just documentation.
- PR #195 requires no reactive change here — it's the formalization of a convergence dotKnowledge's own §9 already anticipated (2026-07-12 entry), not a new direction. No conflict with this week's other changes (sources/ledger/journal, `local:`, `rises:`).

## What was tried and abandoned
Nothing abandoned this session.

## Open threads
- [ ] Skill-promotion mechanism (client-specific → shared/agency) — real gap, not yet designed.
- [ ] PR #195 not yet merged — revisit once it lands to actually adopt `log.md` for `.knowledge`'s own affirmation events, rather than leaving FOUNDRY's existing per-file `## Affirmations` pattern (which predates and is separate from this spec's own still-undesigned mechanism) as the only working example.

## Related
- [[2026-07-13-temporal-record-split-and-local-field]] — same day, the sources/ledger/journal split this skills question builds on.
- `GoogleCloudPlatform/knowledge-catalog` PR #195 (public) — read, not linked from the public spec beyond the citation already in §9.

---
type: Journal
date: 2026-07-12
session: capsule-scale-prep-audit
status: shipped
related: []
---

# Pre-scale audit: dotKnowledge sharpened ahead of real capsule builds

## Context
Tucker is about to build real `.knowledge` capsules at scale over the next few days — his own `tucker.knowledge`, upgrading `cosi-knowledge` into `cosi.knowledge`, and several real `client.knowledge` capsules. Rather than build against whatever state the spec happened to be in, ran a parallel audit across dotKnowledge, Throughline, cosi-knowledge, and client-knowledge-template to find what's solid, what's a real gap, and what needs a real decision before building. This entry covers dotKnowledge's own findings and fixes; the other three repos are tracked in their own knowledge layers or, for cosi-knowledge/client-knowledge-template, in FOUNDRY (they don't yet have their own `knowledge/` layer in the dotKnowledge sense).

## What changed
- §4: the document-level `status:` (record affirmation) and §5's manifest-level `status:` (subject lifecycle) share a name but are unrelated axes — added a one-line disambiguation on each so an implementer doesn't write one shared enum validator.
- §7: named which field the confidence gate actually thresholds — `confidence:`'s ordinal band, since every document carries one; `score:` (optional, absent-means-not-computed) is a refinement the gate MAY also consult, never a requirement.
- §9: added two open questions that were real gaps, not just unstated preferences — capsule-approver identity (ported the role-reference framing already proven in FOUNDRY's own decision-record-affirmation spec: never hardcode a name, resolve a role live against the mounting console's actual identity system), and where the affirmation event itself is supposed to live (§3's bundle layout doesn't reserve a file for it — flagged for reconciliation once knowledge-catalog#148's `log.md`-based resolution-event convention lands upstream, rather than inventing a second unlocated mechanism for the same shape of problem).

## Decisions made
None — these are documentation clarifications and honestly-stated open questions, not new design positions. The confidence-gate and status-disambiguation fixes state what the spec's own existing field definitions already implied; the two new open questions name gaps the audit found, they don't resolve them.

## What was tried and abandoned
Nothing abandoned this session.

## Open threads
- [ ] §6's `rises:` vocabulary (`filtered` vs `de-identified`) is still genuinely ambiguous — both values are defined using "de-identified" circularly, and this is the actual boundary deciding what leaves a `client.knowledge` capsule into a shared layer. Real leak risk once real convergence happens. Needs Tucker's call on intent before it's fixed, not a documentation pass — flagged back to him, not resolved here.
- [ ] The capsule-approver-identity and affirmation-event-location open questions just added should get resolved (or at least narrowed) once real client capsules exist and someone actually needs to affirm a decision in one.
- [ ] Convergence-runtime mechanics (§7) remain a sketch, not a spec to implement against — no mount registry, no stated location for the derived/shared layer itself, no format for the gate/audit trail. Fine to design live during the first real convergence per this repo's own "harden from real use" philosophy, but worth knowing going in.

## Related
- Touched articles: none yet — `wiki/` doesn't exist in this repo's `knowledge/` layer yet.

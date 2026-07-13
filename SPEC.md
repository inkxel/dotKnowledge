# .knowledge — Specification

> **DRAFT / RFC v0.** Unstable. Every section is provisional; sections marked *(draft)* especially so. This spec is written to harden from real-world use — expect breaking changes.

## 1. Overview

A `.knowledge` bundle is a directory (or repo) of plain files describing a **single subject** (a `person`, an `org`, a `brand`, or a `project` — see §2). It is **self-describing** (it carries its own metadata and boundary), **portable** (any conforming reader can use it), and **sealed** (it contains no logic about other bundles).

## 2. Subject types — perspective & authority

Every bundle is keyed to one subject, and the subject's **type** names *what kind of subject it is*. The kind fixes the bundle's *perspective*, and perspective fixes *authority*. This is the load-bearing distinction in the format: it is why the same real-world entity can appear in two bundles at once without contradiction.

**Two perspectives:**

- **First-person** — `person`. The bundle is what *that person* knows and has lived. Experiential, selective (never the full file), and it **decays** — the knowledge fades as the person moves on. A `person` bundle is the source of truth for *"what I know,"* never for *"what is objectively true about X."* It is never handed off; it travels only with its subject.
- **Third-person / canonical** — `org`, `brand`. The bundle is the maintained, objective record of its subject. Exhaustive, kept current while owned, and **handoff-ready** (single-tenant by construction). It is the source of truth for *"what is true about this subject."*

**Types:**

| type | perspective | source of truth for | examples |
|---|---|---|---|
| `person` | first-person · **decays** · never ships | "what I know & lived" | you, a colleague |
| `org` | third-person, canonical | the organization's own record | Mattel, Disney, Collier Simon |
| `brand` | third-person, canonical · nests under an `org` | a brand / account / IP's record | Hot Wheels, Experian, Gargoyles |
| `project` | third-person, canonical · nests under a `brand`/`org` · **freezes** | a single effort's record, at the time it shipped | a game build, a campaign, a code repo |

Only `person` flips perspective to first-person; `org`, `brand`, and `project` are all canonical and differ by **scale** (`org` ⊃ `brand` ⊃ `project`), not perspective. The type field answers *what kind of subject*; perspective is derived from the kind. A `brand` MAY declare a `parent:` org; a `project` MAY declare a `parent:` brand or org.

**The bar for a new type:** a type must name something you would *seal and hand off as its own bundle*. `org`, `brand`, and `project` clear it. A *campaign*, *feature*, or *sprint* does not — a campaign **is** a `project` (developing a campaign is a project; once shipped it crystallizes into reusable assets — see §2.3), and a feature or sprint lives *inside* a project bundle's wiki/journal. This bar keeps the type vocabulary from sliding into an unbounded taxonomy.

**Relationship is orthogonal to type.** Whether a third-person bundle is a *client*, an *in-house* line, an *employer*, or a *partner* is a **relationship**, not a type — the structure is identical either way. Record it on the manifest (§5):

```yaml
relationship: client | in-house | employer | partner
```

So an agency's client and an in-house brand are the **same type** (`brand`), differing only in `relationship:`:

- Experian → `brand`, `relationship: client`, `parent: cosi`
- Hot Wheels → `brand`, `relationship: in-house`, `parent: mattel`
- Collier Simon → `org`, `relationship: employer`
- A client's Q4 campaign → `project`, `relationship: client`, `parent: experian`

This is what lets one format span agency work, in-house roles, and personal life without inventing a new type per employment model.

### 2.1 The overlap rule

A real-world entity may be recorded in two bundles at once — most commonly a `brand` you work on, held both in its own canonical bundle **and** in your `person` bundle. They do not contradict, because they answer different questions:

- the **`brand`** bundle holds *what is true about the brand* — complete, current, third-person;
- your **`person`** bundle holds *what you lived working on it* — your relationships, your reads, the lessons you carried — first-person, partial, decaying.

### 2.2 Lifecycle — mount, unmount, decay

A console (the convergence layer, §7) **mounts** the bundles relevant to current work. While a `brand` bundle is mounted, canonical facts are read *through* to it — never copied into the `person` bundle. On **unmount** (you roll off the account, or leave the org), the canonical bundle detaches or is handed to its next owner; your first-person residue **stays** in your `person` bundle and naturally decays — surviving as compacted digests and journal entries, exactly like memory of a job you left.

**Guardrail.** First-person residue MUST stay distinguishable from canonical record. Use `confidence:` and `last_updated:` (a fading memory is low-confidence and old) so a stale `person`-bundle recollection is never mistaken for a `brand` bundle's current truth. The rule for any reader:

> **Canonical facts → read the `brand` / `org` bundle. Lived experience → read the `person` bundle. Never confuse the two.**

**How residue actually gets written — engagement-triggered, never ambient.** A `person` bundle must never passively absorb a mounted `brand`/`org` bundle's content just because it's reachable — that would violate §2's own "selective, never the full file" definition of first-person residue, and it would go stale silently the moment the canonical bundle updates, with no signal that the copy no longer matches. Residue is written only from an actual act of engagement: a real query against the canonical bundle, and the answer received, is itself a work-activity event — the same "capture what happened while working" mechanism as any other source (a meeting, a thread), not a separate sync process. The novel-and-notebook framing: the canonical bundle is the novel, complete and sitting there; the person bundle is the reader's own notebook, which fills only with what the reader actually looked up and used — never a copy of the whole book. This is also why residue legitimately outlives the mount, and can predate a bundle ever existing at all — a decade at an employer with no `org` bundle of its own still leaves real notebook entries, written once when they mattered, decaying independently ever since, never re-synced to a source that may not even exist.

Engagement is not one-shot: repeated queries against the same fact compound into durable, gist-level familiarity — not a prohibition on remembering any particular *kind* of fact. A frequently-touched fact (a brand's voice, its palette) becomes working knowledge precisely because it's used often enough to earn it; a rarely-touched one (an exact Pantone value, a precise spacing rule) stays a canonical-bundle lookup by default, not because it's off-limits to remember, but because it's rarely engaged enough to compound into residue. Frequency of real use decides this, not a category rule.

### 2.3 Project lifecycle — freeze & crystallize

A `project` is the one canonical type that **completes**, and a brand may run many at once (a brand with three live campaigns), each its own bundle nested under the brand. A project carries `status:` (§5):

- **active** — in flight; the record is current.
- **shipped** — delivered; authority crystallizes at the ship point (*"what was true at ship"*), distinct from the brand's ever-current record.
- **archived** — closed out. Its durable residue is not just frozen text but **operable assets** — the subject-specific `skills/` and `design/` layers it produced (§3): a reusable skill, its design voice, its motion definitions. These can be archived as a layer and/or **promoted up into the parent brand's reusable layers**, so the brand keeps the capability while the moment-in-time project record is sealed.

This is why `project` earns a type and not merely a flag on `brand`: it owns its own decisions, journal, skills, and design layers, freezes as a unit, and archives or hands off independently of the brand it served.

## 3. Bundle layout *(draft)*

```
<subject>.knowledge/
  capsule.yaml        # manifest: identity + boundary policy (required)
  BOUNDARY.md           # human-readable mirror of the boundary (optional)
  wiki/                 # curated content — markdown + frontmatter
  sources/              # raw intake — transcripts, threads, exports (pointers or originals)
  ledger/               # append-only, agent/harness-authored — what changed in this bundle, and why
  journal/              # person-only (§2) — append-only, human-authored first-person record
  decisions/            # ADR-format decision records
  design/               # (optional) brand / design tokens (DTCG)
  skills/               # (optional) subject-specific skills
```

**`sources/` vs. `ledger/` vs. `journal/` — three different voices, not one "journal" pattern wearing different names.** A naive scaffold hands every bundle the same `journal/` folder regardless of subject type; that's a category error worth naming explicitly:

- **`sources/`** — universal, any bundle. Raw intake: meeting transcripts, Slack threads, exports. Nobody's voice — just the material `wiki/` gets curated from and `decisions/` gets grounded in.
- **`ledger/`** — universal. Append-only, third-person, agent/harness-authored: what changed in this bundle and why, session by session. The audit trail an agent keeps of its own work on the subject's behalf — closer to OKF's reserved `log.md` concept than to a narrative.
- **`journal/`** — **`person` bundles only.** First-person, human-authored, decaying (§2). Not `ledger/` under a different name — it's the subject's own voice, and only a `person` has one. An `org`/`brand`/`project` bundle carries no `journal/`; its temporal record is `ledger/` alone.

The spec fixes the *mechanism* here (voice, perspective, append-only-ness) — deliberately not the *cadence*. Whether a `journal/` gets written daily, weekly, or only when something happens is an implementation's own working rhythm, not something this format prescribes.

**`skills/` (and `design/`) — the same "does this belong in the capsule or the console" question, answered by a different test.** `sources/ledger/journal` split by *voice*; `skills/` splits by a different axis: **does this capability make sense independent of which specific subject's capsule it's operating on, or does it only exist because of something particular to *this* subject?**

- **Generic operating mechanism** (a daily-briefing routine, a session-wrap-up ritual, a weekly-planning cadence) works the same way regardless of *which* subject's bundle is mounted — it's a property of the *console*, not the capsule. Stays in the console's own tooling, never in `skills/`.
- **Subject-specific capability** (a brand-voice checker that only makes sense because of one brand's actual voice rules, a person's own writing-voice-matching skill) exists *because of* facts particular to this one subject. It belongs in that subject's own `skills/`, because it's part of what makes the bundle a complete, portable artifact — hand the bundle to a new owner (a client capsule changing hands, per §2.3's "operable assets" on archive) and the skill needs to travel with it, the same way a brand's actual voice guidelines do.

The test isn't "is this a skill" — plenty of subject-specific skills exist. It's "would this capability still make sense if you swapped which subject's capsule was mounted." If yes, it's console tooling. If no — it only works because of *this* subject — it's capsule-resident.

## 4. Document frontmatter *(draft)*

Every markdown document carries YAML frontmatter. Frontmatter **is** the document's metadata — there is no separate metadata store.

```yaml
---
name: short-kebab-slug
type: subsystem | topic | person | decision | ...
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
basis: live-source | authored | partner-attested | vendor-doc | forecast | computed | inferred   # claim provenance
status: proposed | accepted | superseded | deprecated   # record affirmation — applies to type: decision
confidence: high | medium | low | speculative   # ordinal band, human-gradable
score: 0.0-1.0   # optional — present only when actually computed; absent means "not computed," not "hidden"
classification: public | internal | confidential | restricted   # sensitivity; assigned at ingest, enforced downstream
related: [[other-doc]]
---
```

- **`basis:`** — where the claim came from, borrowed verbatim from [knowledge-catalog #151/#159](https://github.com/GoogleCloudPlatform/knowledge-catalog/issues/151)'s reliability enum. Distinct from `status:` — a claim can be `authored` (a person stated it directly) and still be an unaffirmed `status: proposed` record. Confusing the two is the exact bug this split exists to prevent.
- **`status:`** — has *this record* been affirmed by a human, independent of how solid the claim inside it is. Standard ADR values. Automated writers may only ever write `status: proposed`; anything higher requires an append-only affirmation event, never an in-place edit of this field. Only meaningful on `type: decision` documents. **Unrelated field, same name:** the manifest's `status:` (§5) is the *subject's* lifecycle (active/shipped/archived) — a different axis at a different scope. A document can be `status: accepted` inside a `project` bundle whose own manifest is `status: shipped`; don't write one validator that treats these as the same enum.
- **`confidence:`** — an ordinal band a human can grade at a glance; the interoperable filter. No longer overloaded — see `score:`.
- **`score:`** — optional 0.0–1.0 float, present only when a convergence layer's confidence gate has actually computed one. An absent `score` means "not computed," not "hidden." Also the decay signal for first-person residue (§2.2), separate from `confidence`'s human-gradable band.
- **`classification:`** — the sensitivity tag. Declared *with the content* so it travels with the bundle and a reader can enforce access without a central service.

## 5. The manifest — `capsule.yaml` *(draft)*

```yaml
subject: <slug>
type: person | org | brand | project             # the subject kind (§2)
relationship: client | in-house | employer | partner   # canonical bundles only; orthogonal to type (§2)
parent: <slug>                                   # optional; brand→org, project→brand/org nesting
status: active | shipped | archived              # subject lifecycle; projects freeze & crystallize (§2.3) — unrelated to §4's document-level status: (record affirmation), despite the shared name
format_version: 0
rises: never | filtered | de-identified          # the boundary (see §6)
access: open | protected                         # protected → consult a whitelist
local: true | false                              # transport/handling (see §6.1) — true (default) = sovereign, cloneable, offline-capable; false = mediated-runtime only
legal_signoff: false
```

- **`local:`** — a transport/handling property, distinct from `access:`/`rises:` (those govern *who* and *what*; this governs *how the bytes move*). `true` (default): the bundle is a sovereign, cloneable git repo — check it out fully, run it fully offline or against a local model. `false`: the rare bundle that must never leave a mediated runtime unmodified — served byte-scoped, streamed, never cloned raw. Treat `false` as an exception reached for deliberately, not a default posture.

## 6. The `rises:` boundary — the core extension *(draft)*

`rises:` declares what, if anything, may be synthesized **out of** this bundle into a shared/derived layer:

- **`never`** — nothing leaves (e.g. HR, personal, authored canon).
- **`filtered`** — **`person` bundles.** Personal/confidential material stripped, gated by the subject's own **opt-in** — the person chooses what of their own experience feeds the shared layer. Consent is the gate, not just anonymization.
- **`de-identified`** — **`org`/`brand`/`project` bundles.** Generalized, non-identifying patterns may rise once anonymized — there's no individual whose consent is needed, because the subject isn't a person.

The distinguishing question is *whose* consent gates the rise: a `person` bundle's owner opts in per-item (`filtered`); a canonical bundle's patterns rise once they no longer identify anyone (`de-identified`), a mechanical anonymization step, not a consent step.

The bundle **declares** (passive); a convergence layer **enforces** (active). This is the federated-learning shape: silos declare, the aggregator only takes what's permitted.

## 7. The convergence protocol *(draft)*

How a layer mounts N bundles and synthesizes across them while honoring each boundary:

1. **Mount** — register a set of bundles.
2. **Read across** — answer cross-bundle queries, honoring each bundle's `rises:`.
3. **Synthesize** — produce a derived/shared view from only what's permitted to rise.
4. **Gate + audit** — two gates govern writes: a **confidence gate** (human-in-the-loop below a threshold) and a **classification / security agent** (tag, auto-classify, SOC-aligned). Either can block or reroute. Both write an **audit trail**. The confidence gate thresholds `confidence:`'s ordinal band (§4) — every document carries one, so the gate always has a signal to act on. `score:`, when a document happens to have one computed, is a finer-grained refinement the gate MAY also consult, but the gate's baseline behavior never depends on a field that's allowed to be absent. A gate cannot tell a human-affirmed record from an agent-inferred one — that is `status:`'s job, not `confidence:`'s. A `type: decision` document at `status: proposed` may still rise into a derived/shared layer if `rises:` permits it, but it must carry its `status:` with it and the derived layer must render it as a proposal, never as canonical. See [decision-record-affirmation](https://github.com/inkxel/foundry) *(placeholder — link once FOUNDRY's spec has a public counterpart; internal reference: `knowledge/specs/2026-07-09-decision-record-affirmation.md`)*.

Convergence is the *only* writer of the derived/shared layer. Bundles never write to each other.

## 8. OKF conformance + extensions

- **Conformant:** a `.knowledge` bundle is readable as Open Knowledge Format.
- **Extends OKF with:** the subject-type / perspective model (§2), the `BOUNDARY` / `rises:` policy, `classification` metadata, and the capsule profile (manifest + the optional design/skills/decisions layers).

## 9. Open questions

- The mechanical de-identification contract — `filtered` vs. `de-identified` (§6) are now distinct by *who consents*; still open is the actual checklist/algorithm a convergence layer runs to earn the `de-identified` label (what specifically must be stripped or generalized first).
- Versioning + migration of the format.
- **Narrowed 2026-07-13** — which side of the console/capsule line a skill sits on is resolved (§3: does it make sense independent of which subject's capsule is mounted). Still open: how a subject-specific skill that proves broadly useful gets *promoted* into a shared/agency skill library without just copying the client-specific file — the rise mechanism for capability, parallel to `rises:` for knowledge but not the same thing.
- Whether to split `type` into a binary `perspective` (`person` vs `canonical`) plus an orthogonal `kind` (`org`/`brand`/`project`/…) — the v1 evolution if subject-kinds proliferate beyond the current set.
- Whether the convergence protocol ships a reference runtime or stays protocol-only.
- **Capsule-approver identity.** §4 requires an append-only affirmation event to move a `type: decision` document's `status:` past `proposed`, but the spec never says *who* is authorized to write one, and the manifest (§5) has no approver/owner field. The identity must never be hardcoded into the capsule — a name written into `capsule.yaml` (`approver: jane-doe`) goes stale the moment that person leaves the role. The correct shape is a role reference the capsule stores, resolved dynamically against whatever the mounting console's actual source of truth for role-holders is (a roster, an org chart, an RBAC system) — not a name the format itself owns or freezes. What affirmation needs from that system is one thing: a live answer to "who currently holds the approver role for this capsule," asked at the moment of affirmation, not stored in advance. If no one currently holds that role, decisions in the capsule stay `proposed` forever — the correct and honest outcome, not a bug to route around. How role-resolution actually works is the mounting console's architecture to own, not this format's to prescribe.
- **Where does the affirmation event itself live?** §4's append-only event has no stated address — §3's bundle layout doesn't reserve a file for it, unlike OKF's own `log.md` (§7 of the OKF spec, a reserved bundle-history file). [knowledge-catalog#148](https://github.com/GoogleCloudPlatform/knowledge-catalog/issues/148)'s convergence is now a concrete PR — [PR #195](https://github.com/GoogleCloudPlatform/knowledge-catalog/pull/195) formalizes `supersedes`/`contested_by` with the resolution event's home pinned to `log.md`, keyed to stable concept ids (§120), and explicitly composes with the `proposed | accepted | superseded | deprecated` axis without either side bending — the same axis this spec's `status:` field already uses. Once merged, `.knowledge`'s own `status:` affirmation event should follow the same `log.md` convention rather than inventing a second, unlocated mechanism for the same shape of problem. Not yet merged as of 2026-07-13 — still open until it lands.

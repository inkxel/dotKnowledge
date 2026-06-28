# .knowledge — Specification

> **DRAFT / RFC v0.** Unstable. Every section is provisional; sections marked *(draft)* especially so. This spec is written to harden from real-world use — expect breaking changes.

## 1. Overview

A `.knowledge` bundle is a directory (or repo) of plain files describing a **single subject** (a `person`, an `org`, or a `brand` — see §2). It is **self-describing** (it carries its own metadata and boundary), **portable** (any conforming reader can use it), and **sealed** (it contains no logic about other bundles).

## 2. Subject types — perspective & authority

Every bundle is keyed to one subject, and the subject's **type** fixes its *perspective* — and perspective fixes *authority*. This is the load-bearing distinction in the format: it is why the same real-world entity can appear in two bundles at once without contradiction.

**Two perspectives:**

- **First-person** — `person`. The bundle is what *that person* knows and has lived. Experiential, selective (never the full file), and it **decays** — the knowledge fades as the person moves on. A `person` bundle is the source of truth for *"what I know,"* never for *"what is objectively true about X."* It is never handed off; it travels only with its subject.
- **Third-person / canonical** — `org`, `brand`. The bundle is the maintained, objective record of its subject. Exhaustive, kept current while owned, and **handoff-ready** (single-tenant by construction). It is the source of truth for *"what is true about this subject."*

**Types:**

| type | perspective | source of truth for | examples |
|---|---|---|---|
| `person` | first-person · **decays** · never ships | "what I know & lived" | you, a colleague |
| `org` | third-person, canonical | the organization's own record | Mattel, Disney, Collier Simon |
| `brand` | third-person, canonical · nests under an `org` | a brand / account / IP's record | Hot Wheels, Experian, Gargoyles |

A `brand` bundle MAY declare a `parent:` org. `project` is reserved as an optional finer grain *under* a brand (a single product, campaign, or effort) but is not a required type.

**Relationship is orthogonal to type.** Whether a third-person bundle is a *client*, an *in-house* line, an *employer*, or a *partner* is a **relationship**, not a type — the structure is identical either way. Record it on the manifest (§5):

```yaml
relationship: client | in-house | employer | partner
```

So an agency's client and an in-house brand are the **same type** (`brand`), differing only in `relationship:`:

- Experian → `brand`, `relationship: client`, `parent: cosi`
- Hot Wheels → `brand`, `relationship: in-house`, `parent: mattel`
- Collier Simon → `org`, `relationship: employer`

This is what lets one format span agency work, in-house roles, and personal life without inventing a new type per employment model.

### 2.1 The overlap rule

A real-world entity may be recorded in two bundles at once — most commonly a `brand` you work on, held both in its own canonical bundle **and** in your `person` bundle. They do not contradict, because they answer different questions:

- the **`brand`** bundle holds *what is true about the brand* — complete, current, third-person;
- your **`person`** bundle holds *what you lived working on it* — your relationships, your reads, the lessons you carried — first-person, partial, decaying.

### 2.2 Lifecycle — mount, unmount, decay

A console (the convergence layer, §7) **mounts** the bundles relevant to current work. While a `brand` bundle is mounted, canonical facts are read *through* to it — never copied into the `person` bundle. On **unmount** (you roll off the account, or leave the org), the canonical bundle detaches or is handed to its next owner; your first-person residue **stays** in your `person` bundle and naturally decays — surviving as compacted digests and journal entries, exactly like memory of a job you left.

**Guardrail.** First-person residue MUST stay distinguishable from canonical record. Use `confidence:` and `last_updated:` (a fading memory is low-confidence and old) so a stale `person`-bundle recollection is never mistaken for a `brand` bundle's current truth. The rule for any reader:

> **Canonical facts → read the `brand` / `org` bundle. Lived experience → read the `person` bundle. Never confuse the two.**

## 3. Bundle layout *(draft)*

```
<subject>.knowledge/
  capsule.yaml        # manifest: identity + boundary policy (required)
  BOUNDARY.md           # human-readable mirror of the boundary (optional)
  wiki/                 # curated content — markdown + frontmatter
  journal/              # append-only dated entries
  decisions/            # ADR-format decision records
  design/               # (optional) brand / design tokens (DTCG)
  skills/               # (optional) subject-specific skills
```

## 4. Document frontmatter *(draft)*

Every markdown document carries YAML frontmatter. Frontmatter **is** the document's metadata — there is no separate metadata store.

```yaml
---
name: short-kebab-slug
type: subsystem | topic | person | decision | ...
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
confidence: high | medium | low | speculative   # also where a convergence confidence-gate score lands
classification: public | internal | confidential | restricted   # sensitivity; assigned at ingest, enforced downstream
related: [[other-doc]]
---
```

- **`confidence:`** — how solid the claim is; doubles as the score a convergence layer's confidence gate writes (below a threshold → human-in-the-loop). Also the decay signal for first-person residue (§2.2).
- **`classification:`** — the sensitivity tag. Declared *with the content* so it travels with the bundle and a reader can enforce access without a central service.

## 5. The manifest — `capsule.yaml` *(draft)*

```yaml
subject: <slug>
type: person | org | brand                       # the subject type (§2)
relationship: client | in-house | employer | partner   # org/brand only; orthogonal to type (§2)
parent: <org-slug>                               # optional; for brand bundles that nest under an org
format_version: 0
rises: never | filtered | de-identified          # the boundary (see §6)
access: open | protected                         # protected → consult a whitelist
legal_signoff: false
```

## 6. The `rises:` boundary — the core extension *(draft)*

`rises:` declares what, if anything, may be synthesized **out of** this bundle into a shared/derived layer:

- **`never`** — nothing leaves (e.g. HR, personal, authored canon).
- **`filtered`** — only de-identified, personal/confidential-stripped material, **opt-in**.
- **`de-identified`** — generalized, non-identifying patterns may rise.

The bundle **declares** (passive); a convergence layer **enforces** (active). This is the federated-learning shape: silos declare, the aggregator only takes what's permitted.

## 7. The convergence protocol *(draft)*

How a layer mounts N bundles and synthesizes across them while honoring each boundary:

1. **Mount** — register a set of bundles.
2. **Read across** — answer cross-bundle queries, honoring each bundle's `rises:`.
3. **Synthesize** — produce a derived/shared view from only what's permitted to rise.
4. **Gate + audit** — two gates govern writes: a **confidence gate** (human-in-the-loop below a threshold) and a **classification / security agent** (tag, auto-classify, SOC-aligned). Either can block or reroute. Both write an **audit trail**.

Convergence is the *only* writer of the derived/shared layer. Bundles never write to each other.

## 8. OKF conformance + extensions

- **Conformant:** a `.knowledge` bundle is readable as Open Knowledge Format.
- **Extends OKF with:** the subject-type / perspective model (§2), the `BOUNDARY` / `rises:` policy, `classification` metadata, and the capsule profile (manifest + the optional design/skills/decisions layers).

## 9. Open questions

- The exact `rises:` vocabulary and how `filtered` is operationalized (the de-identification contract).
- Versioning + migration of the format.
- How subject-specific skills compose with shared/agency skills.
- Whether `project` graduates from a reserved finer grain (§2) to a first-class type.
- Whether the convergence protocol ships a reference runtime or stays protocol-only.

# .knowledge — Specification

> **DRAFT / RFC v0.** Unstable. Every section is provisional; sections marked *(draft)* especially so. This spec is written to harden from real-world use — expect breaking changes.

## 1. Overview

A `.knowledge` bundle is a directory (or repo) of plain files describing a **single subject** (a person, project, client, or org). It is **self-describing** (it carries its own metadata and boundary), **portable** (any conforming reader can use it), and **sealed** (it contains no logic about other bundles).

## 2. Bundle layout *(draft)*

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

## 3. Document frontmatter *(draft)*

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

- **`confidence:`** — how solid the claim is; doubles as the score a convergence layer's confidence gate writes (below a threshold → human-in-the-loop).
- **`classification:`** — the sensitivity tag. Declared *with the content* so it travels with the bundle and a reader can enforce access without a central service.

## 4. The manifest — `capsule.yaml` *(draft)*

```yaml
subject: <slug>
format_version: 0
rises: never | filtered | de-identified     # the boundary (see §5)
access: open | protected                    # protected → consult a whitelist
legal_signoff: false
```

## 5. The `rises:` boundary — the core extension *(draft)*

`rises:` declares what, if anything, may be synthesized **out of** this bundle into a shared/derived layer:

- **`never`** — nothing leaves (e.g. HR, personal, authored canon).
- **`filtered`** — only de-identified, personal/confidential-stripped material, **opt-in**.
- **`de-identified`** — generalized, non-identifying patterns may rise.

The bundle **declares** (passive); a convergence layer **enforces** (active). This is the federated-learning shape: silos declare, the aggregator only takes what's permitted.

## 6. The convergence protocol *(draft)*

How a layer mounts N bundles and synthesizes across them while honoring each boundary:

1. **Mount** — register a set of bundles.
2. **Read across** — answer cross-bundle queries, honoring each bundle's `rises:`.
3. **Synthesize** — produce a derived/shared view from only what's permitted to rise.
4. **Gate + audit** — two gates govern writes: a **confidence gate** (human-in-the-loop below a threshold) and a **classification / security agent** (tag, auto-classify, SOC-aligned). Either can block or reroute. Both write an **audit trail**.

Convergence is the *only* writer of the derived/shared layer. Bundles never write to each other.

## 7. OKF conformance + extensions

- **Conformant:** a `.knowledge` bundle is readable as Open Knowledge Format.
- **Extends OKF with:** the `BOUNDARY` / `rises:` policy, `classification` metadata, and the capsule profile (manifest + the optional design/skills/decisions layers).

## 8. Open questions

- The exact `rises:` vocabulary and how `filtered` is operationalized (the de-identification contract).
- Versioning + migration of the format.
- How subject-specific skills compose with shared/agency skills.
- Whether the convergence protocol ships a reference runtime or stays protocol-only.

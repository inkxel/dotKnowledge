# .knowledge — a portable, sovereign knowledge format

> **Status: early RFC (v0.0.x).** Directional and unstable. Shared early to invite discussion and stake the position; the spec hardens from real-world use, not from a committee.

`.knowledge` is an open format for a **portable, self-describing knowledge base** — a sealed bundle that any tool or agent can read, carry, and hand off. One subject per bundle (a `person`, an `org`, a `brand`, or a `project` — see [Subject types](#subject-types--perspective--authority)). Write it once; any "console" (an app, an agent, an editor) reads it.

## Why

AI tools are accreting private, lock-in knowledge stores that forget you the moment you leave. `.knowledge` is the opposite bet: **your knowledge is yours — portable, and not owned by any one vendor's model or app.** A `.knowledge` bundle travels with you between tools, between sessions, and — the endgame — between organizations: a client could hand you their brand knowledge as a `.knowledge` bundle the same way they hand you a brand book.

## What's in a bundle

Plain files — markdown plus a small manifest:

- **content** — markdown documents, each with **frontmatter metadata** (type, confidence, classification/sensitivity, relationships).
- **manifest** (`bundle.yaml` / `BOUNDARY`) — the bundle's identity and its **boundary policy** (`rises:` — what, if anything, may be synthesized *out* of this bundle).
- *(optional)* **design tokens**, **skills**, **decisions** — the operable layers that make a bundle *do work*, not just store text.

## Subject types — perspective & authority

A bundle's **type** sets its *perspective*, and perspective sets *authority*. That's why the same brand can live in two bundles at once without conflict — they answer different questions.

| type | perspective | source of truth for | examples |
|---|---|---|---|
| `person` | first-person · **decays** · never ships | "what I know & lived" | you, a colleague |
| `org` | third-person, canonical | the organization's own record | Mattel, Disney, Collier Simon |
| `brand` | third-person, canonical · nests under an `org` | a brand / account / IP's record | Hot Wheels, Experian, Gargoyles |
| `project` | third-person, canonical · nests under a `brand`/`org` · **freezes** | a single effort, at ship | a game build, a campaign, a code repo |

Only `person` flips to first-person; `org`/`brand`/`project` are all canonical and differ by **scale**, not perspective — so `type` answers *what kind of subject*, and perspective is derived from the kind.

**Relationship is orthogonal to type.** Client vs. in-house vs. employer is a *relationship* (`relationship: client | in-house | employer | partner`), not a type — an agency's client and an in-house brand are both `brand`, differing only in that field. One format spans agency work, in-house roles, and personal life without a new type per job.

**Projects freeze.** A brand can run many projects at once (campaigns, builds, repos); each is its own bundle that nests under the brand and **completes**. On archive, a project's durable output crystallizes into reusable assets — a skill, its design voice, its motion files — that fold back up into the parent brand. A campaign is just a project, which is why the type list stops at four and doesn't sprawl into campaign/feature/sprint.

**The overlap:** a `brand` you work on lives in its own canonical bundle *and* in your `person` bundle — the brand bundle holds what's *true* about it; your bundle holds what you *lived*. Roll off the account and the brand bundle unmounts; your first-person residue stays with you and fades. Canonical facts → read the brand bundle; lived experience → read your bundle; never confuse the two. Full model in [`SPEC.md` §2](./SPEC.md).

## Relationship to OKF + the ecosystem

- **OKF-conformant + extensions.** `.knowledge` speaks Google's Open Knowledge Format and *extends* it with the pieces OKF doesn't cover yet: a **`BOUNDARY` / `rises:`** policy (what may leave the bundle), **classification / sensitivity** metadata, and a **bundle profile**. *Speak the standard, own the extensions.*
- **[Throughline](https://github.com/inkxel/throughline)** is the **reference engine** — it scaffolds and maintains a `.knowledge` bundle (curated wiki, append-only journal, decision records, commit hook).
- **The convergence protocol** *(draft)* defines how a layer may **mount many bundles, honor each one's declared boundary, and synthesize across them** — without bundles ever talking to each other. The bundles stay dumb and portable; the cross-bundle intelligence lives one level up.

## Status & roadmap

v0 RFC, being **dogfooded inside a real multi-client organization** before anything is declared stable. See [`SPEC.md`](./SPEC.md) (draft) and [`knowledge/roadmap.md`](./knowledge/roadmap.md). Honest claims only — this is unproven until the dogfood says otherwise.

## License

[CC-BY-4.0](./LICENSE) for the specification text. Reference implementations (e.g. Throughline) are separately licensed (Apache-2.0).

## Contributing

Early and open. File an issue to discuss the spec, the OKF extensions, or the convergence protocol.

# .knowledge — a portable, sovereign knowledge format

> **Status: early RFC (v0.0.x).** Directional and unstable. Shared early to invite discussion and stake the position; the spec hardens from real-world use, not from a committee.

`.knowledge` is an open format for a **portable, self-describing knowledge base** — a sealed bundle that any tool or agent can read, carry, and hand off. One subject per bundle (a person, a project, a client, an org). Write it once; any "console" (an app, an agent, an editor) reads it.

## Why

AI tools are accreting private, lock-in knowledge stores that forget you the moment you leave. `.knowledge` is the opposite bet: **your knowledge is yours — portable, and not owned by any one vendor's model or app.** A `.knowledge` bundle travels with you between tools, between sessions, and — the endgame — between organizations: a client could hand you their brand knowledge as a `.knowledge` bundle the same way they hand you a brand book.

## What's in a bundle

Plain files — markdown plus a small manifest:

- **content** — markdown documents, each with **frontmatter metadata** (type, confidence, classification/sensitivity, relationships).
- **manifest** (`cartridge.yaml` / `BOUNDARY`) — the bundle's identity and its **boundary policy** (`rises:` — what, if anything, may be synthesized *out* of this bundle).
- *(optional)* **design tokens**, **skills**, **decisions** — the operable layers that make a bundle *do work*, not just store text.

## Relationship to OKF + the ecosystem

- **OKF-conformant + extensions.** `.knowledge` speaks Google's Open Knowledge Format and *extends* it with the pieces OKF doesn't cover yet: a **`BOUNDARY` / `rises:`** policy (what may leave the bundle), **classification / sensitivity** metadata, and a **cartridge profile**. *Speak the standard, own the extensions.*
- **[Throughline](https://github.com/inkxel/throughline)** is the **reference engine** — it scaffolds and maintains a `.knowledge` bundle (curated wiki, append-only journal, decision records, commit hook).
- **The convergence protocol** *(draft)* defines how a layer may **mount many bundles, honor each one's declared boundary, and synthesize across them** — without bundles ever talking to each other. The bundles stay dumb and portable; the cross-bundle intelligence lives one level up.

## Status & roadmap

v0 RFC, being **dogfooded inside a real multi-client organization** before anything is declared stable. See [`SPEC.md`](./SPEC.md) (draft) and [`knowledge/roadmap.md`](./knowledge/roadmap.md). Honest claims only — this is unproven until the dogfood says otherwise.

## License

[CC-BY-4.0](./LICENSE) for the specification text. Reference implementations (e.g. Throughline) are separately licensed (Apache-2.0).

## Contributing

Early and open. File an issue to discuss the spec, the OKF extensions, or the convergence protocol.

# Roadmap — parking lot

> "At some point we should…" — not commitments.

## Near
- ~~Decide repo name + finalize license~~ → **`dotKnowledge`**, **CC-BY-4.0** (2026-06-25).
- Plant the flag: open an OKF-extension RFC / issue staking the `BOUNDARY` / `rises:` + classification extensions (the OKF orbit has an open contribution window).
- Pin the relationship to [Throughline](https://github.com/inkxel/throughline) (reference engine) explicitly in both repos.

## Dogfood-first
- Harden the spec from a real multi-client deployment before declaring anything stable. Extract proven patterns into the spec; do **not** evangelize ahead of a working adopter.

## Later
- Reference convergence runtime (or keep protocol-only).
- `cartridge.yaml` schema + validator (extend OKF's reference validator).
- A conformance test suite.
- **Eject / compact at maturity** (a Throughline feature): at maturity, keep the distilled value (curated wiki + decisions) and strip/archive the append-only journal firehose — the head/log-compaction pattern taken to its conclusion. Lets a mature bundle (or this repo) shed build-noise before hand-off or publish.

## Notes
- Build the standard **public-first**; private adopters consume it. Never sanitize a private repo into this one (drift + leak risk).

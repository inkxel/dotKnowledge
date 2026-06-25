---
type: Decision
date: 2026-06-25
status: accepted
deciders: Tucker
related: [[roadmap]]
---

# Decision: Build `.knowledge` public-first as an open standard (OKF-conformant + extensions), not as a sanitized fork of a private repo

## Context
`.knowledge` is the universal layer beneath a private multi-client knowledge architecture being dogfooded inside a real organization. Two ways to take it public: (a) sanitize the private repo into a public one, or (b) build the standard public-first and have private adopters consume it. The OKF (Open Knowledge Format) orbit is early with an open contribution window — a category-leadership opportunity if a portable, boundary-aware knowledge format is staked now.

## Decision
Build the standard **public-first** in this repo: the `.knowledge` format + OKF extensions (the `BOUNDARY`/`rises:` policy, classification metadata, the cartridge profile) + a draft convergence protocol. No private/organizational data ever enters this repo. [Throughline](https://github.com/inkxel/throughline) is the reference engine. CC-BY-4.0 for the spec text. Dogfood privately → extract proven patterns into the spec; plant the flag early (thin RFC) and harden from real use.

## Consequences
- **Positive:** no sanitization-fork drift or leak risk; clean separation of standard (public) from implementation/data (private); category-leadership window; network-effect endgame (others can deliver knowledge *as* `.knowledge`).
- **Negative:** maintaining a public spec is real work; evangelizing ahead of a proven adopter would over-reach (mitigated by dogfood-first).
- **Neutral:** "speak the standard, own the extensions" applied at the repo level.

## Dissent / Alternatives Considered
- **Sanitize the private repo into a public one** — rejected: permanent drift + leak risk.
- **Fold the standard into Throughline** — rejected: a standard should be tool-neutral; Throughline is the *reference engine*, not the spec's owner.
- **Wait until fully proven before any public artifact** — partially rejected: plant a thin flag early for priority (OKF window), harden from use.

## Sources
- [[journal/2026-06-25-scaffold]]

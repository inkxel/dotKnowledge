# dotKnowledge — Knowledge Layer

> **What this is.** The build-memory layer for the `.knowledge` open standard. `.knowledge` is a portable, OKF-conformant format for a self-describing knowledge bundle — see the repo `README.md` + `SPEC.md`.

This file is read first by any agent session that opens `dotKnowledge/knowledge/`. The standard itself lives in `README.md` + `SPEC.md`; this layer records *how and why the spec is evolving*, session to session.

---

## The doc surfaces (don't confuse them)
- **`README.md` + `SPEC.md`** (repo root) — *the authored standard.* What `.knowledge` is, and the spec itself. The product. (Spec repos keep the spec at root for discoverability; ancillary guides, if any, go in `docs/`.)
- **`knowledge/`** (this layer) — *build memory.* Append-only journal, decision records, and a curated wiki of how the spec got to where it is. For agents + future-you, not for spec readers.
- **[Throughline](https://github.com/inkxel/throughline)** — the reference *engine* that scaffolds/maintains a `.knowledge` bundle. A separate repo; this layer is dogfooded *with* it.

> This `knowledge/` layer is itself a candidate for **eject/compact at maturity** (see `roadmap.md`) — a public standard repo wants the distilled wiki + decisions long-term, not the raw firehose.

## How it's organized
```
knowledge/
  CLAUDE.md     this file — orientation, the three rules, the formats
  wiki/         curated reference, append-only context logs
  journal/      per-session entries — YYYY-MM-DD-slug.md, written continuously
  decisions/    atomic decision records — ADR format
  research/     findings, evaluations, option-analysis
  roadmap.md    parking lot
```

`wiki/` is the curated layer. `journal/` + `decisions/` are the firehose. `research/` holds findings. `roadmap.md` is the parking lot.

---

## The three rules — no more

### Rule 1 — Journal *continuously*, wiki at the end
The journal is the firehose — write to it **as you go, not at session end.** Batching to the end loses information (you forget, you compress, or the session crashes). The journal must survive a crash at any point. Checkpoint after a larger move, a longer run, **right after each `git commit`** (the breadcrumb hook automates the prompt), or before a risky operation. A rough running entry beats a perfect one that never gets written; entries are append-only. **Wiki, by contrast, changes only via an explicit end-of-session compile pass** (per Rule 2) — that keeps it intentional. *Journal hot and often, compile to wiki cold and deliberately.*

### Rule 2 — Wiki updates require a real trigger — only three
1. A new subsystem/capability got added → new article or section.
2. A previously-documented decision is now contradicted → update + log the contradiction.
3. The user explicitly said "document this in the wiki."

No "just in case" updates.

### Rule 3 — Append-only, wiki-links everywhere
Wiki articles are append-only context logs with dated entries. Journal and decision entries are append-only. All cross-references use `[[wiki-links]]`. Never edit prior entries; if something is wrong, append a correction with a link to the contradicting source.

---

## Journal entry format — ADR-flavored

```markdown
---
type: Journal                      # OKF-required concept type (keep it)
date: YYYY-MM-DD
session: short-slug
status: in-progress | shipped | abandoned | superseded-by [[YYYY-MM-DD-slug]]
related: [[article-1]], [[article-2]]
---

# Session Title — What we worked on

## Context
Where we were when we started. What problem this session was responding to.

## What changed
- Paths touched / behavior shipped / subsystems affected: [[article]]

## Decisions made
- **Decision** — short statement. Rationale + link: [[decisions/YYYY-MM-DD-slug]]

## What was tried and abandoned
- Tried X — dropped because Y.

## Open threads
- [ ] Next-session item with [[wiki-link]]
```

## Decision record format — standard ADR

```markdown
---
type: Decision                     # OKF-required concept type (keep it)
date: YYYY-MM-DD
status: accepted | proposed | deprecated | superseded-by [[YYYY-MM-DD-slug]]
deciders: <names>
related: [[article-1]]
---

# Decision: One-sentence statement (verb-led, decisive)

## Context
The forces at play. What we were deciding against. Why now.

## Decision
What we're doing.

## Consequences
- **Positive / Negative / Neutral**

## Dissent / Alternatives Considered
Options weighed before the decision and why each lost. If none, say so explicitly.

## Sources
- [[journal/YYYY-MM-DD-slug]]
```

## Wiki article frontmatter convention
```yaml
---
name: short-kebab-slug
type: subsystem | topic | meta | roadmap
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
confidence: high | medium | low | speculative
related: [[other-article-1]]
---
```

**`confidence:`** — **high** (in code / ratified / repeatedly confirmed) · **medium** (one source, not contradicted) · **low** (single source, provisional) · **speculative** (extrapolation, revisit before acting).

---

## Context log

### 2026-06-25 — Knowledge layer created
Scaffolded with the `knowledge-layer` skill on the repo's founding session. The standard (`README.md` + `SPEC.md`) was authored first; this build-memory layer was added to track the spec's evolution. Seeded with the public-first standard decision ([[decisions/2026-06-25-public-first-standard]]) and a backfill journal entry ([[journal/2026-06-25-scaffold]]). No source code yet (spec repo) — codemap is N/A until reference tooling lands.

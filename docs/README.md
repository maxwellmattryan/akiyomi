# Akiyomi docs

Status: Draft · Last updated: 2026-10-21

This folder is the working record of what Akiyomi is, what we're building first, and why. Most of it was written before any code exists, so expect it to change. When a doc and the code disagree, fix whichever one is wrong.

## Start here

1. [Vision](product/vision.md): what Akiyomi is and the principles it won't trade away.
2. [MVP (0.1.0) — "Scout"](product/mvp.md): assess houses in one prefecture, then plan and arrange the trip to go see them.
3. [1.0.0](product/v1.md): the fuller product that people pay for.
4. [Roadmap](product/roadmap.md): milestones, target dates, and what comes after 1.0.
5. [Open questions](research/open-questions.md): what we don't know yet and how we'll find out.

## Map of the docs

| Path | What's in it |
| --- | --- |
| [product/](product/) | Vision, personas, MVP and 1.0 definitions, roadmap, feature catalog, risks |
| [agent/](agent/) | How the Scout agent works: memory, planning, external APIs, actions and guardrails, evals |
| [data/](data/) | Data sources and their terms, geospatial design, listing schema |
| [engineering/](engineering/) | Proposed architecture and architecture decision records (ADRs) |
| [research/](research/) | Open questions and the spikes that will answer them |
| [glossary.md](glossary.md) | Japanese real estate terms and our own domain vocabulary |

## How we keep these docs

- **Every doc has a status:** `Draft`, `Proposed`, `Accepted` or `Superseded`.
- **Decisions with trade-offs go in an ADR** under [engineering/decisions/](engineering/decisions/), linked from the doc they affect. Copy the [template](engineering/decisions/0000-template.md).
- **Unknowns go in [open questions](research/open-questions.md)** with the spike issue that will answer them. Don't guess in a design doc; link the question.
- **Issues point to docs, and the roadmap points to milestones.** The [roadmap](product/roadmap.md) is the bridge between the two.
- **Use the [glossary](glossary.md) words.** If you need a new term, add it there first.

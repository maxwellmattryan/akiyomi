# 0001. Record architecture decisions

Status: Accepted · Date: 2026-10-21

## Context

Akiyomi starts with many unknowns: data sources, providers, stack, and how far the agent may act. Decisions will be made quickly and revisited. Without a record, we'll re-argue them or forget why a constraint exists.

## Decision

Record every decision with real trade-offs as a short ADR in `docs/engineering/decisions/`, numbered in order, using the [template](0000-template.md). ADRs are never edited after acceptance except to mark them superseded; a change of mind is a new ADR.

## Consequences

- Design docs link to ADRs instead of restating decisions.
- Spike issues end with an ADR (or an explicit "no decision needed").

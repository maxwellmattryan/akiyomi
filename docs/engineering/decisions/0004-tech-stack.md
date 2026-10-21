# 0004. Choose the tech stack

Status: Proposed (open; decide in the stack spike) · Date: 2026-10-21

## Context

See [architecture](../architecture.md) for requirements and options. The firm constraint is Postgres with PostGIS. Everything else should minimize moving parts for a solo builder on a ten-week clock, while supporting durable agent runs, a separate executor, scheduled pipeline jobs and a map-heavy bilingual UI.

## Options

To be filled in by the spike, covering at least: language(s), web framework, managed Postgres provider (PostGIS + pgvector support, Tokyo region), durable workflow approach, queue, hosting for workers that need GDAL, routing engine (hosted vs. self-hosted), email provider, tracing.

## Decision

Pending.

## Consequences

Pending.

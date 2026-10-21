# Architecture

Status: Proposed (nothing decided yet) · Last updated: 2026-10-21

This is a starting proposal so the first spikes have something to push against. The actual choices get made in [ADR 0004](decisions/0004-tech-stack.md) after the stack spike.

## Requirements that shape the architecture

| Requirement | Consequence |
| --- | --- |
| Spatial queries on hazard polygons | Postgres with PostGIS. This is the one firm constraint. |
| Agent runs that pause for days waiting on a user | Durable workflow state in the database, not in memory |
| Strict separation between agent and executor | Two deployables (or at least two processes with different credentials) |
| Batch pipeline (crawl, extract, geocode, overlay) | Scheduled workers with a queue; idempotent jobs |
| Bilingual UI, map-heavy front end | A web framework with good i18n and MapLibre GL |
| Solo builder, ten weeks | Few moving parts, one main language, managed services where cheap |
| Cost visible per Scout and per report | Tracing and cost logging from day one |

## Proposed shape

```
┌──────────────┐     ┌───────────────────────┐     ┌──────────────────────────────┐
│  Web app     │────▶│  App server / API     │────▶│ Postgres                     │
│  (UI, map)   │◀────│  auth, Scout API,     │◀────│  + PostGIS (geo)             │
└──────────────┘     │  approvals            │     │  + pgvector (episodes)       │
                     └──────────┬────────────┘     │  + job/workflow tables       │
                                │                   └──────────────────────────────┘
                 ┌──────────────┼──────────────┐                ▲   ▲   ▲
                 ▼              ▼              ▼                │   │   │
        ┌──────────────┐ ┌─────────────┐ ┌──────────────┐       │   │   │
        │ Scout worker │ │ Pipeline    │ │ Executor     │───────┘   │   │
        │ (planner,    │ │ workers     │ │ (email,      │           │   │
        │  steps,      │ │ (crawl,     │ │  calendar)   │           │   │
        │  adapters)   │ │  extract,   │ │ separate     │           │   │
        └──────┬───────┘ │  geocode,   │ │ credentials  │           │   │
               │         │  overlay)   │ └──────────────┘           │   │
               ▼         └──────┬──────┘                            │   │
        Claude API, provider    │  Object storage (raw HTML/PDF) ───┘   │
        APIs (lodging, flights, └──────────────────────────────────────┘
        routing, geocoding)
```

## Options to decide in ADR 0004

| Layer | Option A (lean) | Option B | Notes |
| --- | --- | --- | --- |
| Language | TypeScript everywhere | TypeScript app + Python pipeline | Python has better GIS and PDF tooling; GDAL's CLI (`ogr2ogr`) covers most ingest from either |
| Web | SvelteKit | Next.js | Either works with MapLibre and i18n |
| Database | Managed Postgres with PostGIS + pgvector (Neon, Supabase, or similar) | Self-hosted on a VM | Check extensions are supported |
| Durable workflows | Postgres-backed state machine (our own tables + a job queue) | Inngest / Temporal / Cloudflare Workflows | Own tables are simplest to inspect and test; a platform buys retries and timers |
| Queue / scheduler | Postgres-based queue (e.g. pg-boss, graphile-worker) | Platform queue | Fewer moving parts with Postgres |
| Routing engine | Hosted API | Self-hosted OSRM/Valhalla on Japan OSM extract | Self-hosting is cheap per call but costs ops time |
| Email | Transactional provider with inbound parsing | — | Pick in the API spike |
| Hosting | One PaaS for app + workers | Edge for web, PaaS for workers | Workers need long runs and GDAL |
| Observability | OpenTelemetry traces + an LLM tracing tool | — | Spans per step, tool call and provider call |

## Cross-cutting

- **Config and secrets:** environment-based; executor credentials only in the executor.
- **i18n:** message catalogs for EN and JA from the first commit.
- **Testing:** unit tests, adapter contract tests with fixtures, eval runner. See [evals](../agent/evals.md).
- **Environments:** local (docker compose with PostGIS), preview per PR, production. Sandbox mode on everywhere except production.
- **Data residency:** prefer a Tokyo region for the database if the provider has one (APPI friendliness, latency to Japanese sources).

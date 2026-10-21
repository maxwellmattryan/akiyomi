# Agent overview

Status: Proposed · Last updated: 2026-10-21

Akiyomi has two pipelines that share one database.

1. **The data pipeline** runs in the background on a schedule. It turns messy sources into listings and assessments. It's the original six-stage pipeline from the brief: collect, normalize, geolocate, assess, estimate, report. It's mostly deterministic, with the model used for extraction, translation and explanation.
2. **The Scout agent** runs per user, interactively. It turns a goal into a plan, runs the plan with tools, learns about the user, and proposes actions. This is where the four agent capabilities live.

```
                     ┌────────────────────── Data pipeline (batch) ───────────────────────┐
 Akiya bank sites ──▶│ collect → extract → translate → geocode → assess (PostGIS + rules) │──┐
 Hazard datasets ───▶│                                       → explain (LLM, cited)       │  │
                     └────────────────────────────────────────────────────────────────────┘  │
                                                                                             ▼
                                                                  ┌──────────────────────────────┐
                                                                  │ Postgres + PostGIS + pgvector│
                                                                  │ listings, findings, profiles,│
                                                                  │ memory, scouts, proposals,   │
                                                                  │ audit log                    │
                                                                  └──────────────────────────────┘
                                                                         ▲          ▲
                     ┌──────────────────── Scout agent (per user) ───────┼──────────┼─────────────┐
 User ⇄ Web app ────▶│ Orchestrator ── Planner (LLM) ── Step runner ─────┘          │             │
                     │      │                  │                                    │             │
                     │      ▼                  ▼                                    │             │
                     │  Memory service    Tools: read · compute · provider · propose│             │
                     │                         │                       │            │             │
                     │                         ▼                       ▼            │             │
                     │                 Provider adapters        Action proposals ───┘             │
                     │           (lodging, flights, routing,           │                          │
                     │            geocoding, hazard APIs)              ▼ (user approves)          │
                     │                                         Executor ──▶ email, calendar       │
                     └────────────────────────────────────────────────────────────────────────────┘
```

## Components

| Component | Job | Doc |
| --- | --- | --- |
| **Orchestrator** | Owns a Scout's lifecycle: creates it, runs steps, checkpoints, pauses for the user, resumes. Durable across restarts. | [Planning](planning.md) |
| **Planner** | LLM call that turns goal + profile + memory into a plan of typed steps, and re-plans when something changes. | [Planning](planning.md) |
| **Step runner** | Executes one step: calls tools, validates outputs against a schema, records cost. Most steps are deterministic code; some call the model. | [Planning](planning.md) |
| **Memory service** | Stores the profile, signals and preference facts; consolidates signals into facts; retrieves the relevant ones for each step. | [Memory](memory.md) |
| **Tools** | The only way the agent touches the world. Four kinds: *read* (search listings, get findings), *compute* (cluster, rank, route), *provider* (call an external API through an adapter), *propose* (create an action proposal). | Below |
| **Provider adapters** | Wrap each external API behind a normalized interface with retries, caching and fixtures. | [External APIs](external-apis.md) |
| **Executor** | Runs approved action proposals. Separate service; the agent has no tool that calls it. | [Actions and guardrails](actions-and-guardrails.md) |
| **Evals** | Score each stage and the agent end to end on every change. | [Evals](evals.md) |

## Tool kinds

| Kind | Examples | Side effects | Who can call |
| --- | --- | --- | --- |
| Read | `search_listings`, `get_assessment`, `get_profile` | None | Planner and steps |
| Compute | `rank_listings`, `cluster_days`, `order_route` | None | Steps |
| Provider | `search_lodging`, `search_flights`, `drive_matrix`, `geocode` | External read-only calls, cost | Steps, within budget |
| Propose | `propose_email`, `propose_calendar_event` | Creates a proposal row; nothing leaves Akiyomi | Steps |
| Memory | `remember_fact`, `ask_to_confirm_fact` | Writes to memory (tier 0) or asks the user | Steps |

There is deliberately **no** `send_email` or `book` tool.

## Models

Use the cheapest model that passes the eval for each job, and pin versions per job so evals are comparable.

| Job | Needs | Starting point |
| --- | --- | --- |
| Planning, re-planning, report explanation | Reasoning, structured output | Mid or top tier Claude model |
| Extraction from HTML/PDF | Structured output, Japanese | Mid tier, with prompt caching on the schema |
| Translation, classification, reply parsing | Cheap, fast, Japanese | Small tier |
| Photo analysis (1.0) | Vision | Mid tier |

The choice per job is recorded in an ADR once evals exist. See [architecture](../engineering/architecture.md).

## A Scout, step by step

1. User writes a goal. The orchestrator creates a Scout and calls the **intake** step, which extracts constraints and asks up to three clarifying questions.
2. The **planner** builds a plan from the step library, using the profile and retrieved preference facts. The plan is validated (known steps, inputs satisfiable, within budget) and shown to the user.
3. The step runner works through the plan. After each step it checkpoints outputs and emits progress to the UI.
4. User interactions (dismiss a house, edit a constraint) become **signals**. Some trigger re-planning of later steps.
5. Steps that want to do something external create **action proposals** and the Scout pauses on them.
6. The user approves, edits or rejects. Approved proposals go to the **executor**. Results come back as events on the Scout.
7. The Scout ends in a state the user can come back to: itinerary, proposals and their outcomes, and what was learned.

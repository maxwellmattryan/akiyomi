# Multi-step planning

Status: Proposed · Last updated: 2026-10-21

The planner turns a vague goal into a sequence of decisions the user can see, change and trust.

## Principles

- **Plans are data, not prose.** A plan is a list of typed steps from a fixed library, validated before it runs. The model chooses and parameterizes steps; it can't invent new ones.
- **Deterministic where possible.** Clustering, routing order, ranking and hazard filters are code. The model plans, extracts, and explains.
- **Visible.** Every step shows its status, inputs, outputs and why it exists.
- **Re-plan locally.** When a step fails or the user changes something, re-plan from that point on. Never restart a Scout from scratch unless the goal changed.
- **Bounded.** Every Scout has budgets for steps, tool calls, tokens and money.

## The step library (MVP)

| Step | Kind | Inputs | Outputs |
| --- | --- | --- | --- |
| `intake` | model | goal text, profile | constraints, up to 3 clarifying questions |
| `search_listings` | read | constraints | candidate listing ids |
| `apply_hazard_filter` | compute | candidates, risk tolerance | kept, dropped (with reasons) |
| `rank` | compute | candidates, profile, facts | ranked list with reasons |
| `await_user_shortlist` | user | ranked list | shortlist (user prunes; creates signals) |
| `cluster_days` | compute | shortlist, trip days, max daily drive | day clusters |
| `route_days` | provider + compute | day clusters, lodging hint | ordered day routes with times |
| `find_lodging` | provider | day clusters, dates, lodging style | lodging options per night |
| `find_flights` | provider | home airport, dates, arrival airport | flight options |
| `build_itinerary` | compute | routes, lodging, flights | itinerary |
| `draft_inquiries` | model | itinerary, listings, contacts, draft preferences | inquiry drafts (EN + JA) |
| `propose_actions` | propose | drafts, itinerary | action proposals (emails, `.ics`) |
| `await_approval` | user | proposals | approved/edited/rejected |

1.0 adds `parse_reply`, `update_itinerary`, `propose_follow_up`, `match_subsidies`, `isochrone_search` and event triggers.

## Plan shape

```
Plan {
  scout_id
  version          // increments on each re-plan
  goal             // structured constraints
  steps: [
    { id, type, params, depends_on[], status, outputs_ref, rationale }
  ]
  budget: { max_steps, max_tool_calls, max_tokens, max_cost_usd }
}
```

`status` is one of `pending`, `running`, `done`, `degraded`, `failed`, `skipped`, `waiting_on_user`.

The planner prompt gets: the step library (name, inputs, outputs, when to use), the structured goal, the profile, retrieved preference facts and episodes, and any prior plan with step statuses. It returns a plan as structured output.

## Validation

Before any plan runs:

- Every step type exists in the library.
- Every step's inputs are produced by an earlier step or available in context.
- The dependency graph has no cycles.
- Required steps are present for the goal type (e.g. a trip goal must include `propose_actions` only after `await_user_shortlist`).
- Estimated cost is within budget.

An invalid plan goes back to the planner once with the validation errors. If it fails again, fall back to the **default plan template** for that goal type.

## Execution and checkpoints

The orchestrator runs steps in dependency order. After each step it writes outputs to the database and the step's status. A crash or deploy mid-Scout resumes from the last checkpoint. `waiting_on_user` steps pause the Scout until the user acts; the UI shows exactly what it's waiting for.

## Re-planning

Triggers:

| Trigger | Example | Re-plan from |
| --- | --- | --- |
| Step `failed` or `degraded` | Lodging provider timed out after retries | That step: retry with a different provider or relax constraints, or mark gap and continue |
| User edits a constraint | "Make it 5 days, not 7" | First step that uses the constraint |
| User prunes shortlist | Dismisses 2 houses | `cluster_days` |
| New memory fact becomes active | "≤20 min to station" confirmed | `rank` (asks first if the shortlist would change) |
| (1.0) External event | Town replies "sold" | `cluster_days` for that day |

The re-plan keeps `done` steps whose inputs didn't change, and shows the user a diff: *"Removed 1 house; Day 2 is now 40 minutes shorter."*

## Day clustering and routing

The core geospatial planning problem: group shortlisted houses into days and order each day.

1. Build a drive-time matrix between houses (and candidate lodging areas) via the routing adapter.
2. Cluster by drive time with a cap of `max_daily_drive` and a max of ~4 viewings per day (viewings take ~45–60 min, plus the town hall office if the akiya bank requires meeting there first).
3. Assign clusters to days, respecting town hall hours (weekdays, roughly 8:30–17:15) and the user's dates.
4. Order stops within each day (small traveling-salesman problem with time windows; brute force or a simple heuristic is enough at ≤5 stops).
5. Suggest lodging near the end of each day's route.

Details in [geospatial](../data/geospatial.md#trip-clustering-and-routing).

## Budgets

| Budget | MVP default |
| --- | --- |
| Steps per plan | 20 |
| Re-plans per Scout per day | 10 |
| Provider calls per Scout | 100 |
| Model tokens per Scout | ~500k |
| Cost per Scout | $2 hard stop, $1 target median |

Hitting a budget pauses the Scout with an explanation, not a silent failure.

## Evals

15 scripted goals (ski house in February from Seattle; Tokyo weekend place by train; impossible goal; contradictory constraints; etc.) with expected properties: plan validity, constraint satisfaction, number of re-plans, cost. See [evals](evals.md).

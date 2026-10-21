# Evals

Status: Proposed · Last updated: 2026-10-21

Evals are part of the build, not a later polish step. Every stage has a hand-checked set and a metric, and CI runs them on every change that touches that stage.

## Eval sets

| Set | Size (MVP) | Built how |
| --- | --- | --- |
| **Listings gold set** | 50 listings from the MVP prefecture, across all sources | Hand-checked fields, hand-placed coordinates with precision, expected hazard findings |
| **Planner goals** | 15 scripted goals with fixed profiles | Written by hand; include impossible, contradictory and edge-case goals |
| **Memory scripts** | 20 signal/conversation sequences | Expected facts, forbidden facts, expected ranking changes |
| **Guardrail red-team** | 30+ cases | Injection in listings and replies, tampered payloads, replayed approvals, rate-limit abuse |
| **Provider fixtures** | Recorded responses per adapter | Captured in the API spikes, plus synthetic failures |

Store sets in the repo (minus anything we're not allowed to redistribute; for those, store IDs and fetch locally).

## Metrics and MVP targets

| Stage | Metric | Target |
| --- | --- | --- |
| Extraction | Field accuracy (price, areas, build year, address, contact) | ≥ 90% |
| Translation | Glossary term consistency; spot-check score | 100% glossary; ≥ 4/5 spot checks |
| Geocoding | Median error vs. hand-placed point; precision label accuracy | ≤ 500 m; ≥ 90% |
| Hazard overlay | Findings match expectations | ≥ 95% |
| Verdict | Every reason cited; rule outcome matches expectation | 100%; ≥ 95% |
| Planner | Valid plan rate; hard-constraint satisfaction; median cost | 100%; ≥ 80%; ≤ $1 |
| Memory | Fact precision / recall; forbidden facts; forget works | ≥ 0.8 / ≥ 0.7; 0; 100% |
| Guardrails | Unapproved sends; injection success | **0; 0** |
| Adapters | Contract tests pass; degraded-not-failed under chaos | 100%; 100% |

## How they run

Model evals cost real money, so they run when something they measure changes, never on a schedule. See [ADR 0005](../engineering/decisions/0005-llm-cost-strategy.md#evals-run-on-change-not-on-a-schedule).

- **Deterministic evals** (rules, adapters with fixtures, guardrail logic) run on every PR. They don't call a model.
- **Model evals** (extraction, planner, memory, drafting) run only on PRs that change prompts, model choice, schemas or that stage's code (CI path filters), plus a manual trigger and before each release. They go through the Batch API. Results are stored with model id, prompt version and cost so runs are comparable.
- **Model-backed guardrail cases** run on every PR that touches the drafter, executor or guardrail code.
- **Regression gate:** a PR that drops a metric below target fails CI unless it updates the target with a reason in the PR.
- **Guardrail evals are never allowed to regress.** No override.

## LLM-as-judge

Used only where there's no ground truth (explanation quality, draft politeness in Japanese). Judges get a rubric, and we spot-check judge scores by hand each time the rubric changes.

## Cost tracking

Every eval run and every production Scout logs model, input/output tokens, cache hits, provider calls and estimated USD. The eval report shows cost next to quality so we can see trade-offs.

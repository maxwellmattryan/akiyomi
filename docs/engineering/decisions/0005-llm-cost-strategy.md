# 0005. Keep LLM spend small and capped by design

Status: Proposed · Date: 2026-10-21

## Context

Akiyomi is self-funded, and model API costs have to stay affordable while other people use it. The MVP is free, so spend isn't tied to revenue until 1.0.

Using a personal Claude subscription (for example `claude -p`) as the backend for other users isn't an option: subscriptions are licensed for individual use, and all users would share one person's rate limits. That route is fine for development only (building, labelling the gold set, generating fixtures).

Prices per million tokens (input / output) when this ADR was written; these change, so check before relying on them:

| Model | Input | Output |
| --- | --- | --- |
| Claude Haiku 5.5 (`claude-haiku-5-5`) | $0.10 | $0.50 |
| Claude Sonnet 5.5 (`claude-sonnet-5-5`) | $2 | $10 |
| Claude Opus 5.5 (`claude-opus-5-5`) | $4 | $20 |

The Message Batches API is 50% off. Cached prompt prefixes are read at roughly a tenth of the input price.

Rough estimates at those prices:

| Work | Haiku 5.5 | Sonnet 5.5 |
| --- | --- | --- |
| Process one listing (extract, translate, explain) | ~$0.002 | ~$0.07 |
| 1,000 listings, once (re-crawls only reprocess changes) | ~$2–4 | ~$35–70 |
| One Scout | ~$0.02–0.03 | ~$0.30–0.50 |
| Planner eval (15 goals), one run | ~$0.50 | ~$5–8 |

The main cost risk isn't per-user spend. It's (a) uncapped usage and (b) model evals running on a schedule whether or not anything changed: 15 Sonnet planner goals nightly for two months is about $450.

## Options

1. **Subscription via `claude -p`.** Rejected: not permitted for serving others, and doesn't scale.
2. **Bring your own API key.** Shifts cost to users, but most buyers don't have one. Possible later for power users; not for the MVP.
3. **Local open-weight models for everything.** No marginal cost, but weaker at Japanese extraction and planning, and more ops work. Kept as a fallback for the batch pipeline only.
4. **Hosted API, designed for low spend with hard caps.** Cheapest model that passes each eval, batch and caching where possible, spend bounded in several places, invite-only MVP.

## Decision

Option 4, with these rules.

### Model per job

Every job starts on the cheapest model. It moves up a tier only when its eval shows the cheaper model misses the target, and the move is recorded in this table (via a new ADR if it changes the cost picture).

| Job | Starting model | Notes |
| --- | --- | --- |
| Extraction (HTML/PDF → schema) | Haiku 5.5 | Sonnet 5.5 if field accuracy < 90% |
| Translation | Haiku 5.5 | Glossary enforced |
| Verdict explanation | Haiku 5.5 | Rules decide; model only phrases cited findings |
| Intake, memory consolidation | Haiku 5.5 | |
| Planner and re-planning | Haiku 5.5 | Likely to need Sonnet 5.5; let the planner eval decide |
| Inquiry drafting (Japanese) | Haiku 5.5 | Sonnet 5.5 if Japanese quality checks fail |
| Reply parsing (1.0), photo analysis (1.0) | Decide with their evals | |

Opus 5.5 isn't used in the MVP unless an eval proves it's needed. The model for each job lives in config, not in code, and every call logs model, tokens, cache hits and cost.

### Batch

Everything that isn't interactive goes through the Batch API: pipeline extraction, translation and explanation, and model eval runs.

### Caching

- Stable content first in every prompt (step library, schemas, glossary, instructions), with prompt caching on that prefix. Check `cache_read_input_tokens` in logs; a zero means something is invalidating the cache.
- Store model outputs by content hash (translations, extractions, explanations) so unchanged inputs are never sent twice.
- Ranking explanations, hazard text and verdict logic are templates and rules, not model calls.

### Hard caps

Spend is bounded in four places, so the worst-case bill is known in advance:

| Layer | MVP value | When hit |
| --- | --- | --- |
| Monthly spend limit on the API account (Anthropic Console) | $50 | Provider stops serving requests |
| Global daily cap in the app | $5/day | New Scouts queue until tomorrow with a friendly message; the pipeline pauses; in-progress Scouts finish |
| Per user | 1 free Scout per account; 10 re-plans/day | Upsell (1.0) or wait |
| Per Scout | $0.50 hard stop, $0.10 target median | Scout pauses with an explanation |

Caps are checked before each model call using estimated cost, and reconciled with actual usage afterwards.

### Invite-only MVP

The MVP launches behind a waitlist. Invites go out in batches small enough that the daily cap holds even if everyone runs a Scout on the same day. The waitlist also measures demand ([Q8](../../research/open-questions.md#q8-what-will-people-pay-for)).

### Evals run on change, not on a schedule

- Deterministic evals (rules, adapters with fixtures, guardrail logic) run on every PR. They're free.
- Model evals run only when a PR changes what they measure: prompts, model choice, schemas, or that stage's code (CI path filters), plus a manual trigger and before each release.
- Model eval runs use the Batch API and report their cost next to their scores.
- Guardrail red-team cases that need a model run on every PR that touches the drafter, executor or guardrail code.

## Consequences

- Expected MVP spend is roughly $10–40 total, including evals, with $50 as the hard ceiling.
- If evals push the planner to Sonnet 5.5, a Scout costs ~$0.30–0.50, and the per-Scout cap and daily cap stay the limits that matter.
- Every model call has to go through the LLM client wrapper so caps and logging can't be bypassed.
- Paid tiers in 1.0 tie spend to revenue: a ~$29 report costs cents to produce.
- Revisit when 1.0 pricing launches, when prices change, or if a local model passes the pipeline evals.

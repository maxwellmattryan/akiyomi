# Roadmap

Status: Draft · Last updated: 2026-10-21

Releases follow semver. Everything before 1.0.0 is `0.x`: the MVP is **0.1.0**. Each release is a set of GitHub milestones, and each milestone groups the issues for one feature area. Dates are targets, and they overlap on purpose.

## MVP — 0.1.0 "Scout"

Definition: [mvp.md](mvp.md). Ten weeks, launching the first week of January 2027.

| Milestone | Target | What it delivers |
| --- | --- | --- |
| [MVP · 0 Research & decisions](https://github.com/maxwellmattryan/akiyomi/milestone/1) | Nov 1 | Prefecture, source terms, providers, stack, user interviews |
| [MVP · 1 Foundations](https://github.com/maxwellmattryan/akiyomi/milestone/2) | Nov 8 | Repo, CI, Postgres + PostGIS, sign-in, LLM client, environments |
| [MVP · 2 Listings pipeline](https://github.com/maxwellmattryan/akiyomi/milestone/3) | Nov 22 | Crawl, extract, translate, geocode; gold set and eval runner |
| [MVP · 3 Map & hazard assessment](https://github.com/maxwellmattryan/akiyomi/milestone/4) | Nov 29 | Hazard overlays, build-era rules, verdict lite, map explorer |
| [MVP · 4 Memory & user model](https://github.com/maxwellmattryan/akiyomi/milestone/5) | Dec 6 | Intake, signals, preference facts, ranking, editable memory |
| [MVP · 5 Travel & maps integrations](https://github.com/maxwellmattryan/akiyomi/milestone/6) | Dec 13 | Adapter framework; lodging, flights, routing, rail |
| [MVP · 6 Scout planner](https://github.com/maxwellmattryan/akiyomi/milestone/7) | Dec 20 | Durable Scouts, planner, shortlist, day trips, itinerary, Scout UI |
| [MVP · 7 Actions & guardrails](https://github.com/maxwellmattryan/akiyomi/milestone/8) | Dec 27 | Proposals, approval UI, executor, inquiries, replies, audit, red-team |
| [MVP · 8 Launch 0.1.0](https://github.com/maxwellmattryan/akiyomi/milestone/9) | Jan 3 | Landing and price test, legal pages, production demo, write-up |

```
            Oct 26   Nov 2    Nov 9    Nov 16   Nov 23   Nov 30   Dec 7    Dec 14   Dec 21   Dec 28
0 Research  ████████
1 Found.         ████████
2 Pipeline            ██████████████████
3 Hazards                      ██████████████████
4 Memory                                ██████████████████
5 Integr.                                        ██████████████████
6 Planner                                                 ██████████████████
7 Actions                                                          ██████████████████
8 Launch                                                                    ██████████████████
```

**Critical path:** prefecture → crawler → geocoding → hazard overlay → shortlist → day clustering → inquiries. If something slips, use the [cut list](mvp.md#cut-list-if-we-fall-behind).

**Build order inside the plan:** get one thin slice working end to end early (one source, ten listings, one hazard layer, a hard-coded profile, a two-step plan, one draft email to yourself in sandbox) before widening any stage. It's much easier to improve a working loop than to integrate nine finished parts in the last week.

## 1.0.0 — "From scouting to deciding"

Definition: [v1.md](v1.md). Dates set after the MVP launch.

| Milestone | What it delivers |
| --- | --- |
| [1.0 · Paid property report](https://github.com/maxwellmattryan/akiyomi/milestone/10) | Legal flags, full report, PDF and sharing, paste-any-URL, Stripe |
| [1.0 · Money & subsidies](https://github.com/maxwellmattryan/akiyomi/milestone/11) | Subsidy extraction, matching, stacking; costs; renovation ranges |
| [1.0 · Towns & discovery](https://github.com/maxwellmattryan/akiyomi/milestone/12) | Town profiles, quiz, isochrones, alerts, three prefectures |
| [1.0 · Long-running Scout](https://github.com/maxwellmattryan/akiyomi/milestone/13) | Reply parsing, event-driven re-planning, follow-ups, resumable Scouts |
| [1.0 · Bookings & permissions](https://github.com/maxwellmattryan/akiyomi/milestone/14) | Booking handoff, spend caps, calendar sync, permissions page |
| [1.0 · Memory & user model v2](https://github.com/maxwellmattryan/akiyomi/milestone/15) | Households, decay, memory explanations |
| [1.0 · Visit companion](https://github.com/maxwellmattryan/akiyomi/milestone/16) | On-site checklist, photo analysis, debriefs |
| [1.0 · Quality, scale & launch](https://github.com/maxwellmattryan/akiyomi/milestone/17) | Review queue, eval dashboard, cost budgets, legal review, launch |

Suggested order: Paid report and Money first (revenue), then Long-running Scout and Towns, then the rest.

## After 1.0.0

Roughly following the extended spec's later releases. These are themes, not commitments; they get milestones when 1.0 ships.

| Theme | Features |
| --- | --- |
| Visit and buy | Document vault, contract and registry explainers, purchase roadmap, professional marketplace |
| Renovate | Scope builder, seismic retrofit estimates, contractor briefs, progress tracker |
| Own and operate | House manual, maintenance planner, remote-owner services, vacation-rental feasibility |
| Inheritors | "I inherited a house" wizard, registration duty check, listing helper |
| Community | Owner stories, town reviews, Q&A, buddy matching |
| Platform | Municipal tier and town matchmaking, data and risk API, investor plan, co-buying pods, change detection, 2050 climate score |

## How the roadmap is maintained

- Every issue belongs to exactly one milestone. Issues use the repo's task template (description + requirements checklist) and link to the doc they implement.
- Labels: `area:*` for the part of the system, `spike` for research that ends in a decision.
- When a milestone's scope changes, update the doc it points to in the same PR.

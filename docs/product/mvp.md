# MVP (0.1.0) — "Scout"

Status: Proposed · Target: public launch the first week of January 2027 · Last updated: 2026-10-21

## One line

> Tell Akiyomi the house you're dreaming of. It finds houses in one prefecture that won't hurt you, plans the trip to go see them, and writes to the towns for you, after you say yes.

## Why this slice

The original brief's v1 was "Assess one prefecture": crawl listings, overlay hazards, produce a cited report. That's still the foundation, but on its own it's a static report generator. The MVP adds the **Visit** stage of the journey on top, because a scouting trip is the smallest piece of the akiya journey that needs all four agent capabilities at once, and it sits on top of real geospatial work:

| Capability | Where it shows up in the MVP |
| --- | --- |
| **Geospatial** | Hazard overlays in PostGIS, rural geocoding with confidence, map explorer, clustering houses into day trips, drive and rail times |
| **Memory and a user model** | A profile built from conversation, preferences learned from saves, dismissals and edits, used to rank houses and shape the trip; all visible and editable |
| **Multi-step planning** | A vague goal becomes a visible plan of typed steps (clarify → shortlist → cluster → route → lodging → flights → inquiries) that re-plans when a step fails |
| **Messy external APIs** | Flights, lodging (Rakuten Travel), routing, multiple geocoders, government hazard APIs; Japanese payloads, rate limits, partial failures |
| **Acting on the user's behalf** | Bilingual viewing inquiries emailed to akiya bank contacts, plus calendar invites; every external action is a proposal the user approves exactly |

It's also the demo that explains itself in two minutes, and the founder needs it for their own house hunt.

## Scope

**One prefecture.** Working assumption: **Nagano** (skiing and onsen appeal for foreign buyers, many akiya, a prefecture-wide akiya bank portal, Shinkansen from Tokyo). To confirm in the [prefecture spike](../research/open-questions.md#q1-which-prefecture).

**One persona done well:** the [overseas dreamer](personas.md#mvp-primary-persona-the-overseas-dreamer), with the [Tokyo resident](personas.md#mvp-secondary-persona-the-tokyo-based-foreign-resident) as a check.

### In

| # | Feature | Summary |
| --- | --- | --- |
| F1 | Listings pipeline | Crawl a handful of akiya bank sources in the prefecture, extract to one schema, translate, geocode with confidence |
| F2 | Map and hazard assessment | Map explorer with hazard layers; per-listing hazard panel; build-era check; a "verdict lite" with cited reasons |
| F3 | Memory and user model | Intake conversation → profile; signals → learned preferences; personalized ranking; "What Akiyomi knows about you" page |
| F4 | Scout planner | Goal → visible plan → shortlist → day clusters → itinerary, with re-planning |
| F5 | Travel and maps integrations | Adapters for lodging, flights, routing and geocoding behind one normalized interface |
| F6 | Actions and guardrails | Action proposals, approval UI, bilingual inquiry emails, calendar export, audit log, undo window |
| F7 | Accounts, evals and launch | Sign-in, EN/JA UI, eval set and runner, cost logging, landing page, disclaimers |

### Out (on purpose)

- **Payments.** The MVP is free. Stripe and paid reports come in [1.0](v1.md). (Trade-off discussed below.)
- **Real bookings.** The agent finds flights and lodging and hands off with prefilled links. It never pays for anything.
- **Full legal flags, subsidies, renovation estimates.** Only the build-era check and hazards are in the verdict lite.
- **Reading replies automatically.** Replies from towns are captured and shown, but the agent doesn't act on them yet (1.0).
- **More than one prefecture, mobile app, phone calls, any brokerage-like behaviour.**

## The demo scenario

This is the acceptance test for the whole MVP. If this works end to end with real data, the MVP is done.

1. **Sam signs in** and types: *"I want a cheap house near skiing in Nagano that I can go see in late February. Under ¥5M. My partner and I both work remotely."*
2. **Intake.** Akiyomi asks at most three clarifying questions (home airport? how much flood risk is OK? how far will you drive in a day?) and saves the answers to Sam's profile, showing each one as it's remembered.
3. **The plan appears** as a checklist: *Find candidates → Check hazards → Rank for you → Group into day trips → Route each day → Find places to stay → Find flights → Draft viewing requests.*
4. **Shortlist.** A map shows ~20 candidates. Hazard checks drop the ones in deep flood or landslide zones (Sam can see why). The rest are ranked for Sam, each with "why it's here". Sam dismisses two with the reason "too far from a station". Akiyomi asks, *"Should I prefer houses within 20 minutes of a station from now on?"* Sam says yes; it's saved as a preference with that dismissal as its source.
5. **Day trips.** Six houses fall into two clusters. Each day gets an ordered route with drive times and viewing windows (town halls are open weekdays, roughly 8:30–17:15).
6. **Lodging and flights.** Two ryokan near the clusters, flights SEA → HND on Sam's dates, Shinkansen to Nagano, rental car. One lodging search fails (provider timeout); the plan marks that step as degraded and retries, and Sam sees it happen.
7. **Inquiries.** Akiyomi drafts three bilingual viewing requests, one per town, addressed to the contact listed on each official akiya bank page. Sam edits one, approves all three. They go out after a 10-minute undo window, with Sam's email as reply-to.
8. **Calendar.** Sam downloads an `.ics` of the trip, with tentative viewing slots marked "pending town reply".
9. **Next week,** Sam asks *"anything new like those?"* and gets results ranked with everything Akiyomi learned last time.

## Feature details and acceptance criteria

### F1 · Listings pipeline

- Crawl 3–5 sources (the prefecture portal plus a few municipal akiya banks) on a schedule, respecting `robots.txt` and each site's terms. Store raw HTML/PDF with fetch time.
- Extract to the [listing schema](../data/listing-schema.md) with a confidence per field.
- Translate title, description and notes JA → EN with a real-estate glossary.
- Geocode with two or more providers; record precision (rooftop / block / chōme / ōaza / municipality) and confidence.
- **Done when:** ≥100 live listings; extraction field accuracy ≥90% and median geocode error ≤500 m on the hand-checked eval set; pipeline runs nightly without babysitting.

### F2 · Map and hazard assessment

- Ingest flood, landslide and tsunami hazard data for the prefecture into PostGIS with source and "as of" dates. See [geospatial](../data/geospatial.md).
- Overlay each listing's point, buffered by its geocode uncertainty, and record findings with citations.
- Build-era rule: pre-June-1981, 1981–2000, post-2000 standard, with "uncertain" for completion years near the boundary.
- **Verdict lite:** Go / Caution / Walk away, decided by rules; top three reasons written by the model; any sentence without a source ID is blocked.
- Map explorer: listings, toggleable hazard layers, listing drawer, link out to the original listing.
- **Done when:** hazard findings match hand-checked expectations on the eval set ≥95%; every verdict reason has a working citation.

### F3 · Memory and user model

- Intake conversation produces a structured profile (budget, region, household, risk tolerance per hazard, home airport, travel dates, max daily drive).
- Capture signals: save, dismiss (with optional reason), open report, edit a draft, approve or reject an action.
- A consolidation job turns signals into **preference facts** with provenance and confidence; low-confidence facts are confirmed with the user before they're used.
- Ranking uses profile + facts and explains itself ("ranked up: 12 min to station; ranked down: near landslide zone").
- "What Akiyomi knows about you": view, edit, forget any fact; export all.
- **Done when:** in the demo scenario, a dismissal reason changes later rankings; every fact shows its source; "forget" removes it from ranking and planning immediately. See [memory](../agent/memory.md).

### F4 · Scout planner

- A **Scout** is one goal with its plan, state and actions. It's durable: close the tab, come back tomorrow, it resumes.
- The planner turns a goal + profile into a plan made only of steps from a fixed step library. Plans are validated before running.
- Steps checkpoint their outputs. A failed or degraded step triggers re-planning of the steps after it, not a restart.
- Geo clustering groups shortlisted houses into day trips by drive time; each day is ordered with viewing time windows.
- Itinerary view combines flights, lodging, routes and viewing slots.
- Hard limits per Scout: tool calls, tokens and cost.
- **Done when:** 15 scripted goals in the planner eval produce valid plans; ≥80% produce itineraries that satisfy all hard constraints. See [planning](../agent/planning.md).

### F5 · Travel and maps integrations

- One adapter interface per provider type (lodging, flights, routing, geocoding, hazard), normalizing to internal types.
- Timeouts, retries with backoff, rate limiting, caching with TTLs, and recorded fixtures for tests.
- Partial results are first-class: the planner gets "here are 3 of 5 results, provider X timed out", not an exception.
- **Done when:** each adapter has contract tests against recorded responses; killing any one provider degrades the Scout instead of failing it. See [external APIs](../agent/external-apis.md).

### F6 · Actions and guardrails

- The agent can't send anything. It can only create an **action proposal**. A separate executor runs approved proposals.
- Approval UI shows the exact payload (recipient, subject, body in EN and JA) and binds the approval to a hash of it. Editing invalidates the approval.
- Recipients for inquiries must come from the official contact on the listing's source page (an allowlist), never from free text or an email reply.
- Emails send after a 10-minute undo window, at most one inquiry per town per user per week, from an Akiyomi address with the user's email as reply-to.
- Sandbox mode (default in development): all external sends go to the user's own address.
- Every proposal, approval, send and failure goes to an append-only audit log. A global kill switch stops all sends.
- **Done when:** red-team tests (prompt injection in listing text and replies, payload tampering, replayed approvals) produce zero unapproved sends. See [actions and guardrails](../agent/actions-and-guardrails.md).

### F7 · Accounts, evals and launch

- Magic-link sign-in; account and data deletion.
- EN/JA UI strings from day one (content can lag in JA for the MVP).
- Eval runner in CI for extraction, geocoding, hazards, planner and guardrails. See [evals](../agent/evals.md).
- Cost logged per pipeline run and per Scout (model, tokens, cache hits, API calls).
- Hard spend caps: API account limit, global daily cap, one free Scout per account, per-Scout budget. See [ADR 0005](../engineering/decisions/0005-llm-cost-strategy.md).
- **Invite-only:** landing page with a waitlist; invites go out in batches small enough that the daily cap holds.
- A placeholder price to test willingness to pay, disclaimers ("information, not brokerage"), privacy policy.
- A public write-up of the agent design, evals and costs.

## Success criteria

| Area | Target |
| --- | --- |
| End-to-end | The demo scenario works on production with real listings, real APIs and a real (approved) inquiry |
| Quality | Eval targets above, published in the write-up |
| Safety | Zero unapproved external actions, in tests and in production logs |
| Cost | Median Scout under $0.10 in model spend; total MVP model spend (including evals) under the $50 ceiling |
| Users | 25 people outside the team complete a Scout; 5 send at least one inquiry |
| Learning | We know which tier people say they'd pay for, from the landing page test |

## Cut list (if we fall behind)

Cut in this order. The demo scenario still works after each cut, just with less in it.

1. Flights adapter → link out to a flight search with prefilled dates.
2. Inbound reply capture → replies go only to the user's own inbox.
3. Automatic preference consolidation → the user confirms every preference manually.
4. Second geocoder → one geocoder plus manual pin fix.
5. Rail routing → driving only (rental car assumption).

Never cut: citations, the approval gate, the audit log, the eval set.

## Trade-offs we made

- **Scout before payments.** The brief wanted to prove people will pay in v1. The MVP instead proves the agent works and measures willingness to pay with a landing page price test. Payments are the first thing in 1.0. If proving revenue before January matters more, swap: move "paid single report" in and push F6's email sending to 1.0, keeping drafts only.
- **Real emails to real town halls.** This is the most impressive and the riskiest part. The guardrails (allowlisted recipients, rate limits, undo window, sandbox by default) exist so we can do it responsibly. See [risks](risks.md).
- **One prefecture.** Depth over breadth: a great experience in Nagano beats a thin one across Japan.

## Timeline

See the [roadmap](roadmap.md) for milestone dates. Rough shape over ten weeks: research and foundations (weeks 1–2), data and map (weeks 2–5), memory (weeks 4–6), integrations and planner (weeks 5–8), actions and guardrails (weeks 7–9), hardening and launch (weeks 9–10). Milestones overlap on purpose.

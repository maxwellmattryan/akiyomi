# Feature catalog

Status: Draft · Last updated: 2026-10-21

Every feature from the extended spec, tagged with the release it's planned for. This is the backlog, not a commitment. **0.1** = [MVP](mvp.md), **1.0** = [1.0.0](v1.md), **Later** = after 1.0 (see [roadmap](roadmap.md#after-100)).

## 1. Discovery and matching

| Feature | Release |
| --- | --- |
| Intake conversation (goal → profile) | 0.1 |
| Map explorer with hazard layers | 0.1 |
| Personalized ranking with explanations | 0.1 |
| Natural-language search ("under ¥3M, 1 hour from Nagano by train, not in a flood zone") | 0.1 (via Scout), 1.0 (standalone search) |
| Lifestyle intake quiz → ranked towns | 1.0 |
| Town-first mode | 1.0 |
| Isochrone search | 1.0 |
| Saved searches and alerts | 1.0 |
| Paste any URL | 1.0 |
| Map layers for rail, onsen, ski resorts, hospitals, supermarkets | 1.0 |
| Deal score, "too good to be true" flags | Later |
| Off-market signals ("ask the town hall") | Later |
| Similar houses | Later |

## 2. Property report

| Feature | Release |
| --- | --- |
| Hazard panel (flood, landslide, tsunami) | 0.1 |
| Build era vs. 1981 and 2000 standards | 0.1 |
| Verdict lite (rules decide, cited reasons) | 0.1 |
| Full verdict card with confidence | 1.0 |
| Storm surge, liquefaction, active faults, volcanic hazard | 1.0 |
| Legal flags (rebuild restriction, urbanization control, farmland, historic district) | 1.0 |
| Land, boundaries, utilities | 1.0 |
| Seller and registry questions (bilingual) | 1.0 |
| PDF export, share link | 1.0 |
| Photo analysis (visible damage) | 1.0 (user photos), Later (all listing photos) |
| Climate outlook, 2050 view | Later |

## 3. Money and total cost of ownership

| Feature | Release |
| --- | --- |
| All-in purchase cost, yearly running costs | 1.0 |
| Subsidy matcher and stacking | 1.0 |
| Price in the user's currency | 1.0 |
| Financing guide | Later |
| FX alerts | Later |
| 10-year scenario model | Later |

## 4. Renovation planner

| Feature | Release |
| --- | --- |
| Rough renovation range by level | 1.0 |
| Scope builder, seismic retrofit estimate, phasing, DIY vs. hire | Later |
| Contractor brief in Japanese, materials sourcing, progress tracker | Later |
| Asbestos and lead risk by era | Later |

## 5. Life in the town

| Feature | Release |
| --- | --- |
| Town profile (population, healthcare, schools, snow, broadband) | 1.0 |
| Foreigner friendliness, community obligations, seasonal reality | 1.0 (light), Later (full) |
| Getting around (station, Shinkansen, airport, car needed?) | 0.1 (in Scout routing), 1.0 (in town profile) |
| Jobs, day in the life | Later |

## 6. Visit and viewing

| Feature | Release |
| --- | --- |
| **Scouting trip planner** (shortlist → day clusters → route → lodging → flights) | 0.1 |
| **Viewing inquiry drafting and approved sending** | 0.1 |
| Calendar export | 0.1 (`.ics`), 1.0 (sync) |
| Reply parsing and re-planning | 1.0 |
| Booking handoff with guardrails | 1.0 |
| On-site inspection checklist (mobile, offline) | 1.0 |
| Trial stays (otameshi jūtaku) links | 1.0 |
| Virtual viewing request with shot list | 1.0 |
| Live translation aid | Later |

## 7. Buying and paperwork

All **Later**: purchase roadmap, document vault, contract explainer, registry decoder, deadline calendar, professional marketplace, remote buying kit. These need legal review before they ship.

## 8. Ownership and operation

All **Later**: house manual, maintenance planner, remote-owner services, vacation-rental mode, disaster preparedness, value tracker.

## 9. Inheritors and sellers

All **Later**: "I inherited a house" wizard, registration duty check, vacant-home designation risk, listing helper, demolition estimator.

## 10. Community and content

| Feature | Release |
| --- | --- |
| Public data analyses (cheapest safe towns, best subsidy towns) for distribution | 0.1 (one post), 1.0 (regular) |
| Owner stories, town reviews, Q&A, buddy matching, expert office hours, course | Later |

## Out-of-the-box bets

All **Later**, most need scale, trust or legal review: co-buying pods, village bundles, renovation preview images, crowd-renovation, work-exchange stays, climate-safe 2050 score, carbon report, satellite change detection, town matchmaking, akiya passport, heirloom finder, kura and kominka spotlight, phone bridge, price-drop prediction, gamified journey, akiya stays marketplace.

**"Buy it for me" agent mode** from the spec is the long-term version of Scout. The MVP and 1.0 build it in safe slices: draft → send with approval → read replies → prepare bookings. It never negotiates or transacts.

## Monetization

"Free to browse, pay for judgment." Prices are placeholders to test, not research.

| Tier / line | What's included | Release |
| --- | --- | --- |
| Free | Map, translated listings, hazard badges, verdict lite, one Scout | 0.1 |
| Single report (~$29) | Full property report, PDF, questions | 1.0 |
| Buyer pass (~$99 / 3 months) | Unlimited reports and Scouts, alerts, comparisons | 1.0 |
| Investor plan (~$79/month) | Bulk scoring, portfolio, export | Later |
| Owner plan, inheritor package | — | Later |
| Referrals, expert calls (disclosed fees) | — | Later |
| Agent/listing-site licence, municipal tier, data API | — | Later |

Rankings never depend on who pays, every paid placement is labelled, and the verdict is never for sale.

# Vision

Status: Draft · Last updated: 2026-10-21

## The pitch

**"Carfax plus a local fixer for Japan's 9 million empty houses."**

Finding a cheap akiya is easy. Knowing whether it's a trap is hard, and getting from "that looks nice" to standing in the house with the town's akiya bank officer is harder still. Akiyomi does both halves:

1. **Assess.** Read every listing the way a careful local would: hazards, seismic era, legal flags, subsidies and costs, each claim cited to its source.
2. **Scout.** Turn a vague dream ("a ski house under ¥5M I could see in February") into a shortlist, a trip, and inquiries sent to the right town halls, with the buyer approving every step that leaves the building.

Assess is the moat: data and rules that are slow to build and easy to trust. Scout is the reason people come back: an agent that remembers what they want and does the tedious work of getting them there.

## The problem

- **Scale.** Japan had roughly 9 million vacant homes in the 2023 Housing and Land Survey, and the number keeps rising.
- **Scattered listings.** Hundreds of municipalities run their own akiya banks, mostly in Japanese, in inconsistent formats, often as PDFs.
- **Hidden risks.** Flood, landslide and tsunami zones; houses built before the 1981 seismic standard; lots where you're not allowed to rebuild; rot, termites and asbestos.
- **Hidden money.** Towns offer renovation and relocation subsidies that buyers never find.
- **The trip is a project of its own.** A viewing means emailing a rural town hall in Japanese, waiting days for a reply, then stitching flights, trains, a rental car and a ryokan around three houses forty minutes apart. Most people give up here.
- **Existing tools curate; they don't assess or act.** Newsletters and listing sites show houses and leave the rest to the buyer.

## Who it's for

Overseas and Japan-based buyers first, especially people who don't read Japanese well. See [personas](personas.md). Professionals, municipalities and data buyers come later.

## Principles

These come from the original spec and are non-negotiable. The agent-specific ones are new.

**Product**

- **Assess, don't advertise.** Every listing gets a verdict with reasons, including "walk away".
- **Every claim cites a source.** Each finding links to the hazard map, PDF page or ordinance it came from, with the date it was checked and a confidence level.
- **Bilingual by default.** Every output exists in English and Japanese, so a buyer can forward it to a city hall clerk unchanged.
- **Honest about uncertainty.** Estimates show ranges. Unknowns become questions, not guesses.
- **Information, not brokerage.** Akiyomi informs and refers. Licensed professionals handle transactions.
- **The house is part of a life decision.** Report on the town, the community and the cost of living too.

**Agent**

- **Rules decide, the model explains.** Hazard overlap, seismic era and zoning are deterministic checks. The LLM writes the explanation and plans the work; it never invents a finding.
- **The agent proposes; the user disposes.** Anything that leaves Akiyomi (an email, a booking, a calendar invite) is an action proposal the user sees exactly and approves. See [actions and guardrails](../agent/actions-and-guardrails.md).
- **Memory is visible and editable.** Users can see everything Akiyomi has learned about them, where it came from, and delete it. See [memory](../agent/memory.md).
- **Plans are visible.** The user can see the agent's plan, what step it's on, and why, and can change it.
- **Be a good guest.** Rural town halls are small offices. Akiyomi never spams them, never sends on behalf of someone who isn't serious, and respects towns that screen buyers.

## The journey

The full product follows a buyer through eight stages. Each release covers more of them.

| Stage | Question | MVP (0.1) | 1.0 | Later |
| --- | --- | --- | --- | --- |
| Dream | Could I live in rural Japan? | Intake conversation | Lifestyle quiz, town-first mode | Community, owner stories |
| Discover | Which towns and houses fit? | Map + ranked listings in one prefecture | Multiple prefectures, isochrone search, alerts | Off-market signals, deal score |
| Assess | Is this house a trap? | Hazard panel, build era, verdict lite | Full paid report, legal flags, subsidies, costs | Photo analysis at scale, climate 2050 view |
| Visit | How do I go see it? | **Scout: plan the trip, send inquiries** | Reply handling, replanning, booking handoff, on-site checklist | Phone bridge |
| Decide and buy | How do I buy it? | — | — | Document vault, contract explainer, professionals |
| Renovate | What will it cost to fix? | — | Rough renovation range | Scope builder, contractors |
| Live / operate | How do I own it from abroad? | — | — | House manual, maintenance, rentals |
| Exit / pass on | What about inherited houses? | — | — | Inheritor wizard |

## Business model (summary)

**Free to browse, pay for judgment.** Listings, hazard badges and a first scout are free. Full reports, unlimited scouts and alerts are paid. Referrals to licensed professionals come later, always disclosed. The verdict card is never for sale. Details and placeholder prices: [feature catalog](feature-catalog.md#monetization).

## Why now, why us

- Government hazard and real estate data have become much more machine-readable (for example MLIT's Real Estate Information Library API).
- LLMs make it practical to read messy Japanese HTML and PDFs, and to run multi-step agents with tool use.
- The founder is a prospective akiya buyer and the first user.

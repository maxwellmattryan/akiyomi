# Personas

Status: Draft · Last updated: 2026-10-21

The extended spec lists 22 personas in five groups. That's the long-term market. The MVP serves **one** primary persona well, because the Scout agent only proves itself if it can take one kind of person all the way from a vague goal to an inquiry sent.

## MVP primary persona: the overseas dreamer

> **Sam, 38, lives in Seattle.** Software job, fully remote. Has skied Hakuba twice and keeps a browser tab of ¥3M farmhouses open. Reads hiragana, not kanji. Partner works remote too and needs fiber. Has one week of vacation in late February and a budget of about ¥5M for the house, more for the renovation.

**Job to be done:** "Help me go from browsing to actually standing in three good houses on one trip, without making a fool of myself with the town hall."

**Why this persona for the MVP:** Sam exercises every part of the product the MVP needs to prove.

| MVP capability | What Sam needs from it |
| --- | --- |
| Geospatial | Houses on a map with flood, landslide and tsunami zones; drive times between houses; clusters a person can visit in a day |
| Memory and user model | Remembers budget, "no heavy flood risk", "needs fiber", "hates long drives", partner's constraints, home airport |
| Multi-step planning | "Ski house I can see in February" → shortlist → route → dates → lodging → flights → inquiries |
| Messy external APIs | Flights from SEA, ryokan near the houses, drive and rail times, rural geocoding |
| Acting on the user's behalf | Bilingual viewing requests sent to the akiya bank only after Sam approves the exact text |

**What Sam pays for later (1.0):** the full report on the two houses they're serious about, and a buyer pass while they're actively looking.

## MVP secondary persona: the Tokyo-based foreign resident

> **Priya, 31, lives in Tokyo.** Rents a small flat, conversational Japanese, wants a weekend place within two hours by train. Can do day trips, so doesn't need flights or much lodging.

Priya uses the same Scout flow with a shorter trip (no flights, maybe no hotel) and stresses transit routing instead of driving. She's a good check that the planner doesn't assume every trip starts with a flight.

## Added in 1.0

| Persona | Why in 1.0 | What they push us to build |
| --- | --- | --- |
| Young Japanese U-turn / I-turn mover | Large domestic market; needs a Japanese-first UI | Town comparison, relocation subsidies, JA-first copy |
| Small investor / flipper | Highest willingness to pay | Saved searches, alerts, comparison, bulk scoring (light) |
| Relocator / retiree | High intent, longer horizon | Town profiles (hospitals, snow, foreigner friendliness) |
| Remote-working family | Strong subsidy fit | Schools, pediatrics, child-rearing subsidies |

## Later

Everyone else from the extended spec, grouped:

- **Buyers abroad:** digital nomad, heritage/diaspora buyer, overseas investor.
- **Buyers in Japan:** Japanese retiree, inheritor of an akiya (urgent, high willingness to pay; gets its own wizard).
- **Builders and operators:** DIY renovator, guesthouse/minpaku operator, homesteader, artist/maker, social entrepreneur.
- **Professionals:** real estate agents, architects/inspectors, scriveners/lawyers/tax accountants (B2B, referrals).
- **Institutions:** municipal akiya bank officers (B2G), researchers, lenders and insurers (data/API).

The full table with core jobs and willingness to pay lives in the original extended spec; the [feature catalog](feature-catalog.md) tags each feature with the personas it serves.

## Anti-personas

People we deliberately don't optimize for, because serving them would hurt the principles:

- **Bulk speculators who want to flip rural stock into vacation rentals** in towns that restrict them. We show community obligations and local rules up front.
- **Anyone who wants the agent to negotiate or close a deal.** That's brokerage, which needs a licence. We hand off.

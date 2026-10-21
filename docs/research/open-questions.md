# Open questions

Status: Living document · Last updated: 2026-10-21

What we don't know yet, why it matters, and how we'll find out. Each question has a spike issue in the "MVP · 0 Research & decisions" milestone. When a question is answered, write the answer here in one line, link the ADR, and move it to **Answered**.

## Open

### Q1. Which prefecture?

**Why it matters:** decides which sources we crawl, which hazard data we ingest, and what the demo trip looks like.

**Working assumption:** Nagano. Skiing and onsen appeal for foreign buyers, lots of akiya, a prefecture-wide akiya bank portal, Shinkansen from Tokyo, and real recent flood history (Typhoon Hagibis, 2019) that makes the hazard story concrete.

**Alternatives:** Yamanashi (near Tokyo, Mt Fuji appeal), Chiba's Bōsō peninsula (coast, near Tokyo, tsunami and flood data), Hokkaido around Niseko (strong foreign demand, but different land and snow issues).

**How to answer:** for each candidate, count live listings, list the akiya bank formats (HTML vs. PDF, how many municipalities), check `robots.txt` and terms, check hazard data coverage, and check whether official contact emails are published (needed for inquiries). Score and pick.

### Q2. What are we allowed to do with each source?

**Why it matters:** scraping and redistribution terms decide what we can store, show and cache.

**How to answer:** for every source in [data sources](../data/sources.md), record: `robots.txt`, terms of use, licence (for datasets), attribution text, and whether commercial use is allowed. Contact one or two municipalities directly to ask if they're happy for listings to be summarized with a link back.

### Q3. Which travel and maps providers?

**Why it matters:** flights, lodging, routing and geocoding are the "messy external APIs" in the MVP; their access, coverage and terms decide what Scout can actually do.

**How to answer:** get sandbox or free-tier access for each candidate in [external APIs](../agent/external-apis.md); run the same 10 queries (lodging near three Nagano towns in February; SEA→HND and LAX→NRT flights; drive times between 10 rural points; 20 rural addresses through each geocoder); record coverage, latency, error rates, pricing, rate limits, caching and display terms. Save responses as fixtures.

### Q4. What stack?

**Why it matters:** everything else waits on it. **How to answer:** the stack spike fills in [ADR 0004](../engineering/decisions/0004-tech-stack.md).

### Q5. Do real buyers want the Scout, or just the report?

**Why it matters:** the MVP bets on the scouting trip being the painful, valuable part.

**How to answer:** talk to 5–10 people who bought or seriously looked at an akiya (forums, Reddit, newsletters, YouTube comments). Ask about their last viewing trip: how they contacted towns, how long replies took, what went wrong. Don't pitch; listen.

### Q6. How will towns react to agent-drafted inquiries?

**Why it matters:** if towns see Akiyomi emails as spam, the feature backfires.

**How to answer:** before launch, send a few real inquiries (founder's own interest) and note response rate and tone. Consider contacting one or two akiya bank offices to explain the product and ask what a good inquiry looks like. Their answer shapes the inquiry template.

### Q7. Where's the legal line?

**Why it matters:** brokerage (宅建業) requires a licence; legal and tax advice have their own rules.

**How to answer:** before 1.0 bookings and the full report, get a short consult with a Japanese real estate lawyer or licensed agent. Questions: is sending viewing inquiries on a user's behalf brokerage? What disclaimers are needed? Is referral revenue OK?

### Q8. What will people pay for?

**Why it matters:** decides 1.0 tiers.

**How to answer:** landing page with a real-looking price for a report and a pass; count clicks and waitlist sign-ups per option.

## Answered

None yet.

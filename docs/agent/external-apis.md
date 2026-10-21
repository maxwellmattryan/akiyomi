# External APIs

Status: Proposed · Last updated: 2026-10-21

Akiyomi depends on APIs it doesn't control, most of them messy: Japanese payloads, inconsistent addresses, rate limits, sandbox environments that behave differently from production, and outages. This doc covers the providers we're considering and the adapter layer that hides their mess from the planner.

> **Nothing here is verified yet.** Provider availability, pricing, coverage and terms change. The [API spikes](../research/open-questions.md#q3-which-travel-and-maps-providers) confirm each one and record decisions in ADRs.

## Provider candidates

| Need | Candidates | Why messy | MVP use |
| --- | --- | --- | --- |
| **Lodging** | Rakuten Travel API (strong rural Japan and ryokan coverage); others to evaluate | Japanese fields, area codes instead of coordinates in some endpoints, vacancy search vs. facility search split, affiliate-link handoff | Search near a point for dates; link out to book |
| **Flights** | Duffel, Amadeus for Developers, others to evaluate | Offer expiry, multi-segment itineraries, fare rules, test vs. live behaviour, provider programs changing | Search offers home airport → Tokyo (or Osaka); link out to book |
| **Driving times** | Self-hosted OSRM or Valhalla on OpenStreetMap; Mapbox; Google Routes | Rural road coverage, snow closures not modelled, matrix size limits | Drive-time matrix for clustering and day routes |
| **Rail / transit** | NAVITIME, Ekispert (駅すぱあと), Google (Japan coverage to verify) | Japan transit data licensing; Shinkansen + local lines + buses; infrequent rural service | Station-to-station times for Tokyo/airport → region |
| **Geocoding** | GSI address search, Digital Agency Address Base Registry, Google, Mapbox | Rural addresses stop at ōaza/aza; kanji numerals; old village names after municipal mergers | Compare ≥2, record precision and confidence |
| **Hazard and zoning** | MLIT Real Estate Information Library API; National Land Numerical Information downloads; hazard map tiles; J-SHIS for seismic | Tile-based APIs, raster-only layers, different "as of" dates per area | Prefer bulk download into PostGIS; API for gaps |
| **Email (send/receive)** | Postmark, Resend, Amazon SES, Cloudflare Email | Deliverability to Japanese government domains, inbound parsing, ISO-2022-JP in replies | Send approved inquiries; capture replies |

## The adapter layer

Every provider sits behind an adapter that implements one interface per provider type.

```ts
interface LodgingProvider {
  search(q: LodgingQuery): Promise<ProviderResult<LodgingOption[]>>
}

type ProviderResult<T> = {
  data: T                // possibly partial
  status: "ok" | "partial" | "unavailable"
  provider: string
  fetchedAt: string
  warnings: string[]     // e.g. "2 of 5 areas timed out", "results are approximate"
  cost?: { calls: number; usd?: number }
}
```

The planner only ever sees normalized types and a `status`. It never sees a stack trace.

### Responsibilities of every adapter

| Concern | Rule |
| --- | --- |
| **Normalization** | Map to internal types: coordinates (WGS84), money (amount + currency), times (ISO 8601 with timezone), Japanese and English names both kept |
| **Timeouts** | Per call, short (e.g. 8 s), with an overall deadline per step |
| **Retries** | Exponential backoff with jitter on timeouts, 429s and 5xx; never retry 4xx validation errors |
| **Rate limiting** | Token bucket per provider and per API key, shared across workers |
| **Caching** | Keyed on normalized query; TTL per provider type (geocode: long; hazard: until dataset update; lodging/flights: minutes); only cache what the provider's terms allow |
| **Fallbacks** | Ordered list of providers per type; next provider on `unavailable` |
| **Partial results** | Return what we have with `status: "partial"` and a warning |
| **Cost** | Count calls and estimated spend; feed the Scout budget |
| **Secrets** | Keys from the secret store; never in logs |
| **Observability** | Span per call with provider, latency, status, cache hit |

### Testing

- **Recorded fixtures:** real responses captured during the spike (with personal data stripped), replayed in tests. Each adapter has contract tests against them.
- **Chaos tests:** inject timeouts, 429s, malformed payloads and empty results; the Scout must degrade, not fail.
- **Live smoke test:** a small nightly job hits each provider's sandbox and alerts on shape changes.

## Known messes to design for

- **Rural addresses.** "長野県北安曇郡白馬村大字北城" can cover many square kilometres. Store precision; buffer hazard checks; let users fix pins. See [geospatial](../data/geospatial.md#geocoding).
- **Municipal mergers.** Listings may use pre-merger village names (Heisei mergers). Keep an alias table.
- **Encodings.** Older municipal pages and some email replies use Shift_JIS or ISO-2022-JP.
- **Dates and eras.** Build years in Japanese eras (昭和56年 = 1981). Normalize in extraction.
- **Prices.** "万円" units, "応相談" (negotiable), "0円" giveaways, and rent-or-buy listings mixed together.
- **Seasonality.** Snow closes roads; routing APIs mostly don't know. Flag winter trips in snow areas and add buffer time.
- **Offer expiry.** Flight offers expire in minutes; the itinerary stores the search, not the offer, and refreshes on view.

# Data sources

Status: Draft · Last updated: 2026-10-21

Every source we plan to use, what we use it for, and what still needs checking. **"Verify"** means the access method, licence or terms haven't been confirmed yet; the [data terms spike](../research/open-questions.md#q2-what-are-we-allowed-to-do-with-each-source) does that and updates this table.

## Listings

| Source | Use | Access | Terms | Release |
| --- | --- | --- | --- | --- |
| Prefecture-wide akiya bank portal (for Nagano: 楽園信州, verify) | Bulk of MVP listings | Crawl HTML | Verify; respect `robots.txt`; link out to originals | 0.1 |
| Municipal akiya bank pages in the prefecture | Listings not on the portal; official contact addresses for inquiries | Crawl HTML and PDFs | Verify per town | 0.1 |
| National akiya bank portals (operated by private companies under an MLIT program) | Cross-check, coverage | Probably link-out only | Verify; likely no scraping | Later |
| User-pasted URLs | Paste-any-URL report | Fetch on demand | User-initiated, single page | 1.0 |

We store raw pages for reprocessing and audit, but **show** users our extracted facts plus a link to the original, not a copy of the listing.

## Hazards and land

| Source | Use | Access | Terms | Release |
| --- | --- | --- | --- | --- |
| National Land Numerical Information (国土数値情報) | Flood, landslide, tsunami zones; administrative boundaries; rail and stations | Bulk download (shapefile/GeoJSON) → PostGIS | Verify licence per dataset (some are commercial-use OK with attribution, some aren't) | 0.1 |
| MLIT Real Estate Information Library API (不動産情報ライブラリ) | Hazard, zoning, urban planning, land prices, facilities via API | API key application | Verify terms and rate limits | 0.1 (gaps), 1.0 (zoning) |
| Hazard map tiles (重ねるハザードマップ) | Map display; raster sampling where vector data is missing | XYZ tiles | Attribution required; verify | 0.1 |
| GSI tiles (地理院タイル) | Base map option | XYZ tiles | Attribution required | 0.1 |
| J-SHIS (NIED) | Active faults, probabilistic seismic hazard | Download / API | Verify | 1.0 |
| Municipal hazard maps (PDF) | Where national data lags | Manual | Per town | Later |

## Geocoding and addresses

| Source | Use | Release |
| --- | --- | --- |
| GSI address search | Free geocoding for Japanese addresses | 0.1 |
| Digital Agency Address Base Registry | Open address dataset for local matching and normalization | 0.1 |
| Commercial geocoder (Google or Mapbox) | Second opinion, confidence by agreement | 0.1 |
| Municipal merger alias table | Old village names → current municipality | 0.1 |

## Travel and routing

See [external APIs](../agent/external-apis.md) for the provider candidates (lodging, flights, driving, rail).

## Towns (1.0)

| Source | Use |
| --- | --- |
| e-Stat census and population projections | Population trend, age mix |
| National open data on medical facilities and schools (via the Real Estate Information Library or NLNI) | Healthcare and school access |
| Japan Meteorological Agency normals | Snow depth, heat days, rainfall |
| Carrier and government broadband coverage maps | Fiber and mobile availability |
| Municipal subsidy pages and PDFs | Subsidy matcher |

## User-contributed (later)

Corrected pins, viewing checklists, photos, debriefs. These become a proprietary dataset over time; collect with clear consent.

## Attribution and freshness

- Every finding stores the dataset name, publisher, version or "as of" date, and the URL it came from.
- The UI shows attribution on the map and "data as of" on every finding.
- A sources page lists all datasets with their licences.

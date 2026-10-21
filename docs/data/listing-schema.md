# Listing schema

Status: Draft · Last updated: 2026-10-21

The normalized shape every source is extracted into. Field names are a starting point; the extraction spike will refine them.

Every extracted field carries `confidence` (0–1) and `source_ref` (raw document id plus a locator: CSS selector, PDF page, or text span).

## Identity and source

| Field | Type | Notes |
| --- | --- | --- |
| `id` | uuid | Ours |
| `source_id` | fk | Which source (akiya bank site) |
| `source_url` | url | Original listing page; we always link out to it |
| `source_listing_id` | text | The source's own number (物件番号), if any |
| `first_seen_at`, `last_seen_at`, `fetched_at` | timestamp | Crawl history |
| `status` | enum | `active`, `under_negotiation`, `sold`, `withdrawn`, `unknown` |
| `raw_document_ids` | fk[] | Stored HTML/PDF snapshots |

## Offer

| Field | Type | Notes |
| --- | --- | --- |
| `offer_type` | enum | `sale`, `rent`, `sale_or_rent`, `free_transfer` |
| `price_jpy` | int? | Normalized from 万円; null if negotiable |
| `price_note_ja` / `_en` | text | e.g. 応相談 (negotiable) |
| `rent_jpy_month` | int? | For rent listings |

## Property

| Field | Type | Notes |
| --- | --- | --- |
| `land_area_m2` | numeric? | Converted from 坪 if needed (1 tsubo ≈ 3.306 m²) |
| `floor_area_m2` | numeric? | Total floor area |
| `building_structure` | enum? | `wood`, `light_steel`, `steel`, `rc`, `other`, `unknown` |
| `floors` | int? | |
| `build_year` | int? | Normalized from Japanese era years |
| `build_year_precision` | enum | `exact`, `approximate` (e.g. 昭和50年代), `unknown` |
| `layout` | text? | e.g. 6DK |
| `includes_farmland` | bool? | |
| `utilities` | object? | water (municipal/well), sewer (public/septic/none), gas (city/propane), from text |
| `condition_notes_ja` / `_en` | text | Seller's notes on repairs needed |

## Location

| Field | Type | Notes |
| --- | --- | --- |
| `address_raw_ja` | text | As listed |
| `address_normalized_ja` | text | After normalization |
| `municipality_code` | text | National local government code |
| `geom` | point | WGS84 |
| `geocode_precision` | enum | `rooftop`, `block`, `chome_or_aza`, `oaza`, `municipality` |
| `geocode_confidence` | numeric | |
| `geocode_sources` | jsonb | Each provider's result and distance from chosen point |
| `user_pin_fix_id` | fk? | If a user corrected the pin |

## Contact (for inquiries)

| Field | Type | Notes |
| --- | --- | --- |
| `contact_org_ja` | text | e.g. ○○町役場 企画課 |
| `contact_email` | text? | Only from the official source page; feeds the [recipient allowlist](../agent/actions-and-guardrails.md#recipient-allowlist) |
| `contact_phone` | text? | |
| `contact_source_url` | url | Page the contact was found on |
| `inquiry_requirements` | text? | e.g. "registration as akiya bank user required first" |

## Content

| Field | Type | Notes |
| --- | --- | --- |
| `title_ja` / `_en` | text | |
| `description_ja` / `_en` | text | Untrusted text; see [prompt injection](../agent/actions-and-guardrails.md#prompt-injection) |
| `photos` | object[] | url, caption, order; stored as links unless terms allow copies |
| `translation_model`, `translated_at` | text, timestamp | |

## Open points

- Many akiya banks require users to register with the town before inquiring or viewing. Capture `inquiry_requirements` and surface it in the Scout so the agent drafts a registration request instead of a viewing request where needed.
- Rent-only listings: include in search (some towns offer rent-to-own) or filter out for MVP? Default: include, filterable.

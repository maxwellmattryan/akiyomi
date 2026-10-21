# Geospatial design

Status: Proposed · Last updated: 2026-10-21

Geospatial work shows up in four places: placing listings on the map, checking them against hazard zones, planning trips between them, and (1.0) searching by travel time.

## Storage

- **Postgres + PostGIS.** Geometries stored as `geometry(…, 4326)` (WGS84). Japan's JGD2011 (EPSG:6668) differs from WGS84 by well under a metre, which is irrelevant at our accuracy, so we reproject on ingest and keep one SRID.
- For distances and buffers in metres, use `geography` casts or a Japan Plane Rectangular CS zone for the prefecture.
- Spatial indexes (GiST) on every geometry column.

## Core tables (sketch)

| Table | Geometry | Notes |
| --- | --- | --- |
| `listings` | `point` | Plus `geocode_precision`, `geocode_confidence`, `geocode_sources` |
| `hazard_zones` | `multipolygon` | `hazard_type`, `class` (e.g. depth band), `dataset`, `as_of`, `source_url` |
| `admin_areas` | `multipolygon` | Prefecture, municipality, ōaza where available |
| `stations` | `point` | Rail stations with line info |
| `findings` | none (FK to listing) | Result of overlays, with citation to `hazard_zones.id` |
| `day_clusters` | `multipoint` | Per Scout |

## Geocoding

Rural akiya addresses are the hardest input in the whole system.

1. **Normalize** the address: full-width to half-width, kanji numerals, era-free, strip building names, map pre-merger names via the alias table.
2. **Query ≥2 geocoders** and the Address Base Registry locally.
3. **Assign precision** from the best match: `rooftop` · `block` (番地) · `chome_or_aza` · `oaza` · `municipality`.
4. **Confidence** from precision plus agreement between sources (distance between their points).
5. **Uncertainty radius** per precision (e.g. block 50 m, aza 500 m, ōaza 2 km, municipality: don't assess).
6. Users (and later, admins) can **fix the pin**; a user fix is stored as a source with its own provenance and is used for that user immediately, and globally after review.

## Hazard overlay

For each listing:

1. Build the uncertainty area: `ST_Buffer(point::geography, radius)`.
2. For each hazard type, intersect with `hazard_zones`.
3. Record findings at two levels:
   - **At the point:** the class at the geocoded point.
   - **Within uncertainty:** the worst class anywhere in the buffer, with the share of the buffer it covers.
4. Rules turn findings into verdict inputs. If the point is clear but 40% of the buffer is a deep flood zone, the verdict says so: *"Possibly in a 3–5 m flood zone; the address is only precise to the aza. Confirm the exact lot."*

### Raster-only layers

Some hazard layers are published only as map tiles. For those, sample the tile pixel colour at the point (and a grid in the buffer) at a fixed zoom and map colours to classes using the legend. This is fragile: record it as lower confidence, pin the tile zoom and legend version, and cover it with eval cases.

## Map UI

- **MapLibre GL** for rendering; base map from GSI tiles or an OSM-based style.
- Hazard layers as vector tiles generated from PostGIS (or served as PMTiles from object storage), plus raster hazard tiles where that's all there is.
- Listings as a clustered point layer; colour by verdict.
- Attribution always visible; "as of" dates in the layer legend.

## Trip clustering and routing

Used by the `cluster_days` and `route_days` steps in the [planner](../agent/planning.md#day-clustering-and-routing).

1. **Drive-time matrix** between shortlisted houses, plus candidate lodging areas and the arrival station, from the routing adapter.
2. **Cluster** with agglomerative clustering on drive time, capped by `max_daily_drive` and ~4 viewings per day. (DBSCAN-style approaches struggle with the hard cap; at ≤20 houses, simple is fine.)
3. **Order** each day: brute force for ≤6 stops, with time windows for town hall hours.
4. **Winter adjustment:** in snow areas between December and March, add a time buffer and flag mountain roads.
5. **Lodging anchor:** search lodging near the last stop of each day and the first stop of the next.

## Isochrones (1.0)

"Houses within 60 minutes of Nagano Station by train, or 30 minutes by car from the ski resort."

- Driving isochrones from the routing engine (Valhalla supports them natively).
- Transit isochrones are harder: start with station-based (rail time to nearby stations + walk/drive from station), which matches how rural Japan actually works.
- Precompute per station for common budgets; compute on demand for custom points.

## Data freshness

Hazard datasets are revised irregularly. Each `hazard_zones` row has `dataset` and `as_of`. When a dataset updates, re-run overlays for affected listings and record the change on the listing's history.

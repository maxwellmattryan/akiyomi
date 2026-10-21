<div align="center">

# Akiyomi 空き家読み

**Due diligence for Japan's vacant houses.**
Find a cheap akiya, then find out if it's a trap: hazards, seismic era, legal flags, subsidies and renovation cost, translated and cited to the source.

[![Status: pre-alpha](https://img.shields.io/badge/status-pre--alpha-orange)](#roadmap)
[![License: AGPL-3.0](https://img.shields.io/badge/license-AGPL--3.0-blue.svg)](LICENSE)
[![Languages: EN | JA](https://img.shields.io/badge/i18n-EN%20%7C%20日本語-red)](#features)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#contributing)

</div>

---

## Why

Japan has roughly **9 million vacant homes** (2023 Housing and Land Survey). Hundreds of municipal *akiya banks* list them, mostly in Japanese and each in its own format. Listing sites show you houses, but none of them tell you:

- whether it sits in a flood, landslide or tsunami zone,
- whether it predates the **1981 seismic standard**,
- whether you're even allowed to **rebuild** on the lot,
- which **town subsidies** would cover half the renovation.

Akiyomi reads all of that for you. *Yomi* (読み) means "reading", as in reading a house's real risks before you buy it.

## Features

> 🚧 = in progress for v1 · 🗓️ = planned

| | Feature | Description |
|---|---|---|
| 🚧 | **Listing aggregation** | Crawls municipal akiya banks, normalizes listings into one schema, and translates them EN ⇄ JA |
| 🚧 | **Verdict card** | Go / Caution / Walk away, with the top three reasons and a confidence level |
| 🚧 | **Hazard panel** | Flood, landslide, tsunami and storm surge overlays from government hazard data |
| 🚧 | **Structure check** | Build year vs. the 1981 and 2000 seismic standards, and what that means for loans and insurance |
| 🚧 | **Legal flags** | Rebuild restrictions, zoning, agricultural land, road access |
| 🚧 | **Paste any URL** | Drop in an outside listing link and get the same report |
| 🗓️ | **Subsidy matcher** | Municipal, prefectural and national grants the buyer qualifies for, with how they stack |
| 🗓️ | **Renovation estimator** | Cost ranges by renovation level, clearly labelled as rough |
| 🗓️ | **Town profiles** | Healthcare, schools, connectivity, snow, community expectations |

Every claim in a report links to its source document, with the date it was checked.

## How it works

```
 Sources                    Pipeline                         Outputs
┌─────────────────┐   ┌──────────────────────────┐   ┌─────────────────────┐
│ Akiya banks     │──▶│ 1. Collect   (crawl)     │   │ Listings + map      │
│ Hazard maps     │   │ 2. Normalize (extract,   │   │ Verdict card        │
│ Zoning / land   │   │              translate)  │   │ Property report     │
│ Subsidy PDFs    │   │ 3. Geolocate (+confidence)│──▶│ Alerts              │
│ User URLs       │   │ 4. Assess    (hazard GIS,│   └─────────────────────┘
└─────────────────┘   │              rules)      │
                      │ 5. Estimate  (cost,      │
                      │              grants)     │
                      │ 6. Report    (cited)     │
                      └──────────────────────────┘
          Evals on a hand-checked listing set run on every change
```

**Design rules**

- **Rules first, LLM second.** Seismic era, zoning and hazard overlap are deterministic checks. The model explains the results; it doesn't decide them.
- **No uncited claims.** Every report sentence is tied to a source ID, and a claim without one is blocked.
- **Compute once, cache forever.** Listings and hazard checks are cached. Only paid reports run expensive model calls on demand.
- **Evals are part of the build.** Each change is scored on extraction accuracy, geocode error, subsidy recall and citation coverage.

## Getting started

> Akiyomi is in early development. Setup instructions will land here with the first runnable pipeline.

```bash
git clone https://github.com/<your-username>/akiyomi.git
cd akiyomi
```

## Roadmap

- [ ] **v1:** one prefecture end to end: crawl → report, paid single report, eval set
- [ ] **v1.5:** subsidy matcher, total cost of ownership, town profiles, more prefectures
- [ ] **v2:** scouting trip planner, document vault, contract and registry explainers, renovation planner
- [ ] **Later:** municipal tools, risk API, 2050 climate hazard score

## Data and responsible use

- Akiyomi respects each source's terms and `robots.txt`, and links out to original listings rather than republishing them.
- Reports are **information, not brokerage or legal advice**. Transactions belong with licensed professionals in Japan.
- Hazard data is attributed to the Japanese government agencies that publish it.

## Contributing

Issues and PRs are welcome, especially from people who have bought or researched an akiya, or who know Japanese real estate law, GIS or municipal data. Please open an issue before starting a large change.

## License

[AGPL-3.0](LICENSE) © Matthew Maxwell

Want to embed Akiyomi in a commercial product without AGPL obligations? Commercial licenses are available. Open an issue or get in touch.
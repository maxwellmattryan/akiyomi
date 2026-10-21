# Glossary

Status: Draft · Last updated: 2026-10-21

Two parts: Japanese real estate terms that appear in listings and reports, and the words we use for our own domain. Use these words in code, docs and UI.

## Japanese real estate and akiya terms

| Term | Japanese | Meaning |
| --- | --- | --- |
| Akiya | 空き家 | A vacant house. |
| Akiya bank | 空き家バンク | A listing service, usually run by a municipality (or a contractor for it), that matches vacant houses with buyers or renters. Formats vary wildly by town. |
| Kyū-taishin / shin-taishin | 旧耐震・新耐震 | The old and new seismic standards. The 1981 standard applies to buildings whose **building confirmation** was issued on or after 1 June 1981, so a completion year of 1981–1983 is ambiguous. |
| 2000 standard | 2000年基準 | June 2000 revisions for wooden houses (joint hardware, wall balance, foundations). Post-2000 wooden houses are generally considered much safer. |
| Building confirmation | 建築確認 | The permit check before construction. Its date, not the completion date, decides which seismic standard applies. |
| Rebuild not permitted | 再建築不可 | A lot where a new building can't be put up, usually because it fails the frontage rule. Cheap for a reason. |
| Frontage rule | 接道義務 | A building lot must touch a road at least 4 m wide for at least 2 m (Building Standards Act, Article 43). |
| Setback | セットバック | When the road in front is narrower than 4 m, part of the lot may have to be given up to widen it. |
| Urbanization control area | 市街化調整区域 | Zone where new building is restricted. Rebuilding or changing use can need permission. |
| Use zoning | 用途地域 | Zoning categories that control what can be built and run (e.g. whether a guesthouse is allowed). |
| Farmland / Agricultural Land Act | 農地・農地法 | Farmland transfers need agricultural committee permission; often bundled with rural houses. |
| Important matters explanation | 重要事項説明 | The disclosure a licensed agent must give before a sale contract. |
| Real estate brokerage licence | 宅地建物取引業 (宅建業) | Licence needed to broker real estate. Akiyomi must never act as a broker. |
| Judicial scrivener | 司法書士 | Professional who handles property registration. |
| Registry / registry extract | 登記 / 登記事項証明書 | Official ownership record and its certified extract. |
| Inheritance registration duty | 相続登記の義務化 | Since April 2024, heirs must register inherited property, generally within three years. |
| Fixed-asset tax | 固定資産税 | Annual property tax. |
| Designated vacant house | 特定空家等 | A neglected house a town designates as a problem; it can lose the residential land tax relief. |
| Minpaku | 民泊 | Private short-term lodging, regulated since 2018. |
| Trial-living house | お試し住宅 | Houses some towns rent short-term so people can try living there. |
| Regional revitalization volunteer | 地域おこし協力隊 | Paid multi-year posts in rural towns; a common path for movers. |
| Neighbourhood association | 自治会・町内会 | Local residents' association with duties like cleanups and snow clearing. |
| Kominka, kura | 古民家・蔵 | Traditional farmhouse; traditional storehouse. |
| Real Estate Information Library | 不動産情報ライブラリ | MLIT service and API for transaction prices, land prices, zoning, hazards and facilities. |
| National Land Numerical Information | 国土数値情報 | MLIT's national GIS datasets (administrative boundaries, rail, hazard zones and more). |
| Overlapping hazard map | 重ねるハザードマップ | The national hazard map portal; publishes hazard map tiles. |
| GSI | 国土地理院 | Geospatial Information Authority of Japan: base maps, tiles, address search. |
| Address Base Registry | アドレス・ベース・レジストリ | Digital Agency's open dataset of Japanese addresses; useful for geocoding. |
| Ōaza / aza / chōme / banchi | 大字・字・丁目・番地 | Parts of a Japanese address. Rural listings often stop at ōaza or aza, which can cover kilometres. |

## Our domain words

| Term | Meaning |
| --- | --- |
| **Source** | A place we collect listings or data from (an akiya bank site, a dataset, an API). |
| **Listing** | One house offered on a source, normalized to the [listing schema](data/listing-schema.md). |
| **Assessment** | The set of findings computed for a listing. |
| **Finding** | One fact about a listing (e.g. "in a 0.5–3 m flood zone"), with a citation, an "as of" date and a confidence. |
| **Citation** | A pointer to the exact source of a finding: dataset and feature, URL and fetch time, or PDF page. |
| **Verdict** | Go / Caution / Walk away, decided by rules from findings; explained by the model. |
| **Profile** | What the user told us directly (budget, household, home airport). |
| **Signal** | Something the user did (save, dismiss with reason, edit a draft, approve an action). |
| **Preference fact** | Something we learned about the user from signals or conversation, with provenance and confidence. |
| **Scout** | One user goal plus its plan, state, results and actions. Durable and resumable. |
| **Goal** | The user's request in their words, plus the structured constraints extracted from it. |
| **Plan** | An ordered set of steps, drawn from the step library, that the planner builds for a goal. |
| **Step** | One unit of work in a plan (e.g. `shortlist`, `cluster_days`, `find_lodging`), with inputs, outputs and a status. |
| **Day cluster** | A group of shortlisted houses that can be viewed in one day. |
| **Itinerary** | The trip: flights, lodging, day routes and viewing slots. |
| **Provider** | An external API (Rakuten Travel, a flight API, a router, a geocoder). |
| **Adapter** | Our code that wraps a provider behind a normalized interface. |
| **Action proposal** | Something the agent wants to do outside Akiyomi (send an email, create an event). Nothing happens until it's approved. |
| **Approval** | The user's consent to one exact action proposal, bound to a hash of its payload. |
| **Action tier** | How risky an action is (0 internal, 1 reversible, 2 external communication, 3 money). Decides what approval it needs. |
| **Executor** | The service that runs approved action proposals. The agent can't call it directly. |

# Changelog

Changes to the public finwire API (`https://public-api.finwire.pl`) visible to API consumers.
All endpoints are under `/v1`. Breaking changes are marked **Breaking**.

## 2026-11-02

### Added

- `GET /v1/prices/retail` (optional `product=<GUS position id>`) - average retail prices of 30 goods and services in Poland from GUS (food, energy, fuel, transport, services), monthly since 2018, with month and year changes, minutes of work at the average net wage and the finwire shopping basket. Dataset `retail-prices`; GUS data CC BY 4.0, attribution "GUS".
- `GET /v1/pensions` - average ZUS old-age pension gross and net, disability, survivor, bridging and social pensions, recipients, payouts, sickness and maternity allowances, pension to wage ratio and monthly history. Dataset `zus-benefits`; preliminary ZUS data, attribution "ZUS".
- `GET /v1/credit-market/demand` (optional `months`) - BIK housing loan demand index (value of inquiries y/y per working day), applicants, average requested amount, granted loans by product, next to NBP new mortgage lending and the NBP lending survey. Dataset `bik-credit-demand`; attribution "BIK" with a link to each press release.
- `GET /v1/knf-warnings` and `GET /v1/knf-warnings/search?q=` - KNF public warnings list (entities reported by KNF for suspected offences), verbatim KNF columns, statistics by legal basis and prosecutor, new entries per week. Dataset `knf-warnings`; attribution "KNF, lista ostrzeżeń publicznych".
- New indices in `/v1/finwire-index`: `finwire_shopping_basket_index` (cost of a 10-item basket of basic food from GUS prices, monthly), `finwire_pension_wage_ratio` (average ZUS pension as % of the average enterprise sector wage, monthly), `finwire_knf_warnings_monitor` (new entries on the KNF warnings list per full week, from 9 November 2026).
- MCP server 1.5.0: tools `get_retail_prices`, `get_pension_stats` and `check_knf_warnings`; `get_credit_market` accepts `topic: "demand"` (BIK). Datasets `retail-prices`, `zus-benefits`, `bik-credit-demand` and `knf-warnings` in `search_datasets` and `get_dataset`.

## 2026-11-01

### Added

- `GET /v1/housing/nbp/{city}` - average offer and transaction prices per sqm on the primary and secondary market in 16 voivodeship capitals and Gdynia, quarterly since Q3 2006 (secondary transactions since 2000 where NBP reports them), from the NBP residential property price database (BaRN), with `primaryMethodBreak` where NBP changed the method (Gdynia). Dataset `nbp-housing-prices`; public NBP data, attribution "NBP, Baza cen nieruchomości mieszkaniowych BaRN".
- `GET /v1/housing/nbp-gap` (optional `market=pierwotny|wtorny`) - offer vs transaction price gap by city and for the NBP 10-city average, quarterly since 2006. MCP: dataset `nbp-housing-prices` in `search_datasets` and `get_dataset`.
- `GET /v1/finwire-index` - registry of all indices computed by finwire.pl, each under a stable id `finwire_*` (also the CSV `series_id` and JSON-LD `identifier` on finwire.pl): latest and previous reading with periods, change and change kind (`pp`, `pct`, `abs`), method version, sources, `stale` flag (last valid reading shown when the source did not respond), `pageUrl`, `methodologyUrl` and a ready `citation`. Indices in preparation are listed with `status: "planned"` and no reading. CC BY 4.0, attribution finwire.pl.
- `GET /v1/finwire-index/{id}` (optional `limit=1-600`, default 120, and `from=YYYY-MM`) - one index with history. Old dataset ids (`credit-market-index`, `credit-stress`, `entry-threshold`, `borrowing-power`, `credit-cost`, `housing-affordability`, `real-deposit-rate`, `purchasing-power`, `nowcast`) are accepted as aliases; `requestedId` echoes the id used. Indices computed from WIBOR publish only the result.
- Field `seriesId` (the `finwire_*` id) in `/v1/index`, `/v1/index/{slug}`, `/v1/index/credit-stress`, `/v1/index/entry-threshold` and `/v1/index/credit-market`.
- MCP server 1.4.0: tools `list_finwire_indices` and `get_finwire_index`; dataset `finwire-index` in `search_datasets` and `get_dataset`. Descriptions of `get_credit_market` and `get_nowcast` name the `finwire_*` id they return.
- `GET /v1/legal-calendar` (optional `kategorie`, `od`, `do`) - legal calendar for personal finance, series `finwire_legal_calendar`: PIT, ZUS and VAT deadlines, year end for IKE and IKZE contributions, changes of statutory values, acts entering into force, RPP meetings and retail bond sales, with the number of changes in acts and values in the next 30 days. ICS subscription: `https://finwire.pl/kalendarz-zmian.ics`. CC BY 4.0, attribution "finwire.pl".
- MCP `get_legal_changes` returns `calendar` (next 30 days of `finwire_legal_calendar`); dataset `legal-calendar` in `search_datasets` and `get_dataset`.
- New indices in `/v1/finwire-index`: `finwire_mortgage_barometer` (lowest, typical and list mortgage margin, rate and APRC, twice a day), `finwire_credit_capacity_index` (median mortgage amount for 5 model households, monthly), `finwire_deposit_rate_index` and `finwire_savings_rate_index` (average of the best term deposits and savings accounts, monthly with a daily reading), `finwire_account_cost_index` (average yearly cost of the cheapest personal account with a card in each bank, monthly), `finwire_rcn_price_index` (transaction prices from notarial deeds, quarterly), `finwire_price_gap_index` (developer list price vs deed price on the primary market, quarterly), `finwire_offer_ticker` (daily count of offer changes), `finwire_demand_index` (median loan amount and flat price entered in finwire.pl calculators, monthly, published above a sample threshold), `finwire_legal_calendar`.
- Existing indicators under `finwire_*` ids: `finwire_credit_market_index`, `finwire_credit_stress_index`, `finwire_entry_threshold_index`, `finwire_nowcast`, `finwire_borrowing_power_barometer`, `finwire_mortgage_cost_barometer`, `finwire_housing_affordability_barometer`, `finwire_real_deposit_rate_barometer`, `finwire_purchasing_power_barometer`.
- `/v1/index/entry-threshold`: `methodVersion` and per city `pricePerM2`, `priceSource` (`rcn` or `gus`), `priceDeeds`, `priceFrom`, `priceTo`, `m2ForMedianWage` (flat size financed by the median wage); `/v1/index/entry-threshold/history`: `methodVersion` and `m2ForMedianWage` per point.
- Docs home and `llms.txt`: section "finwire indices" with every `finwire_*` id, its cadence and aliases, read from the live registry.

### Changed

- Entry threshold (`finwire_entry_threshold_index`) method 2: price per m² from notarial deeds in the Real Estate Price Register (4-quarter window weighted by deeds), GUS RCiWN median only as a flagged fallback for small samples; history recomputed with method 2.
- MCP server instructions point to `list_finwire_indices` and `get_finwire_index` for finwire indices; descriptions of `get_bank_rates`, `get_housing_prices` and `get_legal_changes` name the `finwire_*` ids available through `get_finwire_index`.
- `GET /v1/legal-calendar` documented in OpenAPI (`LegalCalendarResponseDto`).

## 2026-10-29

### Added

- `GET /v1/macro/nowcast` - Finwire Nowcast, the finwire.pl model of the next CPI and core inflation readings (point and 80% interval) and the next RPP decision (probabilities of cut, hold, hike): target period, release and freeze date, status (`live`, `frozen`, `settled`, `expired`), model inputs, settled history against actual readings and backtest and live accuracy scores. CC BY 4.0, attribution "finwire.pl (Finwire Nowcast)".
- `GET /v1/energy/regulated-prices` (optional `date=YYYY-MM-DD`) - household electricity (G11, G12, G12w) and gas (W-1.1 to W-4) tariffs approved by the President of URE in force on the day: default-seller energy prices, distribution rates per operator and gas tariff area, fees, cheaper-zone hours; net amounts with the VAT rate, each row with the URE decision, decision date and PDF. `inForce: false` marks a tariff past its `validTo` with no newer one known yet.
- `GET /v1/brokers` - brokerage account fees from official fee tables: online commission on GPW, US and German stocks, account and currency conversion fees, IKE/IKZE, investor protection, promotions valid today, fee table URL with its effective date and the status of a weekly automatic check.
- MCP server 1.3.0: tools `get_nowcast`, `get_energy_prices` and `get_broker_fees` over the endpoints above; the datasets are also available through `search_datasets` and `get_dataset` (`nowcast`, `energy-tariffs`, `broker-fees`).
- MCP server 1.2.0: 14 new tools over the `/v1` datasets, with the same source whitelist (no WIBOR/POLSTR, no single bank offers): `get_interest_rates`, `get_currency_rates` (incl. gold), `get_fuel_prices`, `get_housing_prices` (RCN cities and districts, or GUS), `get_rents` (with rental yield), `get_housing_costs`, `get_credit_market`, `get_bank_rates` (offer aggregates and retail bonds), `get_legal_limits` (incl. local tax limits), `get_legal_changes`, `get_regional_data`, `get_europe_data`, `search_datasets` and `get_dataset` (any dataset by id). Each result has `outputSchema`, Polish-formatted values, `license`, `citation` and `url` to the finwire.pl page (`utm_source=mcp&utm_medium=assistant&utm_campaign=<tool>`).
- MCP resources `finwire://datasets` and `finwire://dataset/{id}`; prompts `city_housing_report` and `personal_finance_limits`.
- MCP protocol version `2025-11-25` accepted in `initialize`.
- Discovery: `GET /mcp/server-card` (MCP Server Card, `application/mcp-server-card+json`), `GET /.well-known/ai-catalog.json`, `GET /.well-known/mcp.json` and `/.well-known/mcp/server-card.json` (same card). `server.json` manifest for the MCP Registry in this repository.

### Changed

- MCP `tools/list`: `annotations` of every tool now include `title` (same as the top-level `title`).
- Text results of the macro tools end with a "Zobacz na finwire.pl" link. `structuredContent` of existing tools is unchanged.

## 2026-10-28

### Added

- MCP server upgrade: new tools `get_macro_snapshot`, `compare_macro_indicators` (2-4 series of the same frequency, aligned by period) and `get_macro_changes`; every indicator now has ready-made `display`, `periodLabel`, `changeDisplay`, `freshness` (`new`/`current`/`stale`) and `daysToNextRelease`; `list_macro_indicators` takes `include_descriptions`, matches Polish and English words and no longer caps results; unknown keys return "Did you mean" hints; tool results declare `outputSchema`; resources `macro://about`, `macro://catalog`, `macro://indicator/{key}`; prompts `macro_briefing` and `explain_indicator`; links in results carry `utm_source=mcp`. `/mcp` has its own rate limit (600 requests/minute per IP) because AI connectors share cloud IPs.
- `POST /mcp` - Model Context Protocol server (Streamable HTTP, stateless, no key). Tools: `list_macro_indicators` (filter by `group` or `query`), `get_macro_indicator` (latest reading plus history, optional `from=YYYY-MM`), `get_macro_calendar` (optional `limit`). Same data and source whitelist as `/v1/macro` and `/v1/calendar`; each result carries `asOf`, `source` and `citation`.
- `GET /v1/macro/indicators` and `/{key}/history` - about 40 headline indicators of the Polish economy in one table (GDP, retail sales, industrial and construction output, CPI and core inflation, unemployment, wages, NBP rates, FX, household credit, sentiment, legal limits) with the latest reading, change, sparkline and source; full history per indicator, optional `from=YYYY-MM`. WIBOR and POLSTR rows are excluded.
- `GET /v1/macro/consumer-sentiment` - GUS consumer confidence (BWUK, WWUK) and 13 components, monthly history since 2010.
- `GET /v1/wages/enterprise` - GUS average gross wage in the enterprise sector: monthly, real change, employment, by province.
- `GET /v1/series/gold` and `/history?range=1m|1y|5y|max` - NBP gold price: latest quote, 1m/6m/1y/3y/5y changes, all-time high, 12-month average, history.
- `GET /v1/fuel-prices` - maximum fuel prices (Pb95, Pb98, diesel) from the announcements of the Minister of Energy: in force today, announced for tomorrow, history.
- `GET /v1/legal-changes` (optional `days`) and `/process/{term}/{number}` - acts of Dz.U. and Monitor Polski on finance, tax, labour and housing entering into force, and open bills in the Sejm with their last stage.
- `GET /v1/income/municipalities` and `/counties` (optional `year`) - residents income per capita by municipality and county from the local-government share in PIT (Ministry of Finance Rb-27S, GUS BDL), with rank and change vs the previous year.
- `GET /v1/housing/upkeep` and `/{city}` - monthly running costs of a flat in 20 cities (GUS BDL): charges, heating, electricity, gas, water, waste.
- `GET /v1/housing/europe`, `/overburden` and `/overcrowding` - Eurostat house price index ranking (Poland, EU, euro area) and housing cost overburden and overcrowding rates by country.
- `GET /v1/credit-market/survey` (optional `quarters`) - NBP bank lending survey: credit criteria, demand, housing loan terms and drivers.
- `GET /v1/credit-market/monetary` (optional `months`) - NBP household loans, deposits and new loans, monthly.
- `GET /v1/credit-market/ecb-rates` (optional `product`, `period`) and `/ecb-rates/series` (optional `product`, `areas`, `from`) - ECB MIR average interest rate on new loans by country.
- Invalid optional parameters of the new endpoints return `400` with a short message.

## 2026-10-19

### Removed

- **Breaking** - WIBOR and POLSTR are no longer available. finwire.pl has no license to redistribute GPW Benchmark data; the earlier license note ("Public NBP / GPW Benchmark data") was wrong.
  - `/v1/series/interest-rate` and `interestRates` in `/v1/snapshot` return NBP policy rates only.
  - `/v1/series/interest-rate/wibor-*` and `/polstr-*` return 404.
  - `rates` (WIBOR 3M) removed from `/v1/index/credit-stress` and `creditStress` in `/v1/snapshot`.
  - Barometer `methodology` no longer lists the current interest rate.
  - To use WIBOR or POLSTR, contact GPW: dystrybucja.informacji@gpw.pl.

## 2026-10-15

### Added

- `GET /v1/series/bond-yields` - latest 10-year government bond yields for Poland, Germany, the euro area and the EU, with PL-DE and PL-EA spreads in basis points (Eurostat).
- `GET /v1/series/bond-yields/{geo}` - monthly yield history for `PL`, `DE`, `EA` or `EU27_2020`, optional `since=YYYY-MM`.
- `GET /v1/housing/transactions` - flat transaction prices per m² from notarial deeds (Real Estate Price Register, RCN) in 17 cities: median, quartiles, primary vs secondary market, transaction count, YoY change, quarterly trend.
- `GET /v1/housing/transactions/{city}` - 12-month median price per m² by district, with the difference vs the city median.
- `GET /v1/rent` and `GET /v1/rent/{city}` - asking rents per city and district: median, mean, per m², month-over-month change.
- `GET /v1/rent/yield` - gross and net rental yield and payback period per city.
- `GET /v1/calendar` - upcoming Polish macro releases and Monetary Policy Council (RPP) decisions, with Warsaw time, category and importance.
- `GET /v1/index/entry-threshold` and `/history` - the gross monthly wage needed for a mortgage on a model flat within KNF DSTI limits in 17 cities, the installment and the share of employees earning at least that; daily history for a single person or a couple.
- `GET /v1/series/unemployment`, `/counties` and `/history` - GUS registered unemployment rate for Poland and the 16 provinces, counties with the lowest and highest rate, monthly history since 2011.
- `GET /v1/wages/occupations` and `/{code}` - GUS wages by occupation: mean, median, deciles, by gender and sector, gender pay gap.
- `GET /v1/housing/supply` and `/{city}` - GUS housing supply and demand: dwellings per 1000, completions, permits, population and migration, yearly since 2010.
- `GET /v1/legal-limits/local-tax` - upper limits of property tax rates per tax year with the announcing act.
- POLSTR history: `/v1/series/interest-rate/polstr-*` now go back to 2021 (GPW Benchmark yearly files; values before June 2025 are the administrator's pre-production history). Previously the series started in September 2026.
- NBP exchange rate history: `/v1/series/fx/{code}` returns up to 10 years (`days` up to 3650); previously the history started in 2021.
- Barometers (`/v1/index`, `/v1/index/{slug}`): `history` with the best and worst month since 2015 and `betterThanPct`, public `id` and `ref`, and `pageUrl` linking to the barometer page.

### Changed

- **Breaking** - `/v1/index/{slug}`, `/v1/index` and `barometry` in `/v1/snapshot` return a fixed, documented set of data fields:
  - the internal `slug` (e.g. `oplacalnosc-lokat`) is replaced by the public `id` (e.g. `real-deposit-rate`) and `ref`;
  - `methodology` items are `{ term, definition }` (were `{ k, v }`);
  - `series` items are `{ month, value }`;
  - page-only content is no longer included (FAQ, intro texts, calls to action, calculator and scenario data).
  The OpenAPI schema now matches the responses (it previously documented `headline` and an object `gauge`, which were never returned).
- **Breaking** - `/v1/series/housing` and `/v1/series/housing/{city}` return a fixed set of price fields: `secondaryM2`, `primaryM2`, `secondaryYoyPct`, `primaryYoyPct`, `secondaryRealYoyPct`, `history` of the latest quarters and `priceSource`. Page-only content (verdict, scores, FAQ) is no longer included. The data source is GUS; documentation previously named NBP BaRN. For a longer price trend use `/v1/housing/transactions`.

### Fixed

- Documentation examples used the invalid indicator id `nbp_rate`; the NBP reference rate id is `stopa-referencyjna`.

## 2026-09-25

### Fixed

- Unknown identifiers and invalid parameters (e.g. a typo in `{indicator}`) return `404` instead of `503`.
- `days` below 7 on history endpoints is raised to 7 instead of failing.
- `/v1/series/interest-rate/{indicator}` documentation lists the valid ids.

## 2026-06-23

### Fixed

- `meta.sourceUrl` of the barometer datasets points to the current barometer pages on finwire.pl.

## 2026-06-06

### Added

- First public release: interest rates (NBP, WIBOR, POLSTR), CPI, NBP FX rates, housing prices, retail government bonds, GUS wages, IKE/IKZE limits, deposit, savings, mortgage and cash loan rate aggregates, the credit stress map, barometers and `/v1/snapshot`.
- `{ data, meta }` envelope with the source, license, `asOf` and a ready-to-use `meta.citation` in every response.
- OpenAPI 3 specification with field-level response schemas, interactive reference, dataset pages and `llms.txt`.

### Fixed

- `meta.asOf` of `/v1/snapshot` is the date of its oldest component instead of the request time.

# Changelog

Changes to the public finwire API (`https://public-api.finwire.pl`) visible to API consumers.
All endpoints are under `/v1`. Breaking changes are marked **Breaking**.

## 2026-10-28

### Added

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

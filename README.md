# finwire API - free Polish financial data API (NBP, GUS, RCN)

**finwire API is a free, open REST API with financial, labour market and real estate data for
Poland: NBP policy rates, CPI inflation, NBP exchange rates, 10-year
government bond yields, flat transaction prices from notarial deeds, rents, housing supply,
wages by occupation, unemployment by province and county, and an economic calendar.** No API key, JSON over HTTPS, OpenAPI 3 specification, every response with its
primary source and a ready-to-paste citation. Built and maintained by [finwire.pl](https://finwire.pl).

[![Docs](https://img.shields.io/badge/docs-public--api.finwire.pl-3b82f6)](https://public-api.finwire.pl)
[![OpenAPI](https://img.shields.io/badge/OpenAPI-3.0-22c55e)](https://public-api.finwire.pl/openapi.json)
[![Data license](https://img.shields.io/badge/data-CC%20BY%204.0-orange)](https://creativecommons.org/licenses/by/4.0/)

```bash
curl "https://public-api.finwire.pl/v1/series/interest-rate/stopa-referencyjna?days=90"
```

- **Base URL:** `https://public-api.finwire.pl`
- **Auth:** none - no key, no sign-up, GET only, CORS enabled (works from the browser)
- **Format:** JSON, UTF-8, `{ data, meta }` envelope
- **Specification:** [openapi.json](https://public-api.finwire.pl/openapi.json) · [interactive reference](https://public-api.finwire.pl/reference) · [llms.txt](https://public-api.finwire.pl/llms.txt) · [dataset pages](https://public-api.finwire.pl/datasets)
- **Data license:** CC BY 4.0 for finwire indicators and aggregates; public data requires attribution to the original source; the license of each dataset is in its `license` field
- **WIBOR and POLSTR:** not available. They are GPW Benchmark data; redistribution requires a Market Data License Agreement with GPW ([gpwbenchmark.pl](https://gpwbenchmark.pl/dane-biezace))
- **Changes:** [CHANGELOG.md](CHANGELOG.md)

> **Po polsku:** darmowe API z polskimi danymi finansowymi - stopy NBP,
> inflacja CPI (GUS), kursy walut NBP, rentowności obligacji, ceny transakcyjne mieszkań
> z Rejestru Cen Nieruchomości, czynsze i rentowność najmu, podaż mieszkań, wynagrodzenia
> GUS (także według zawodów), bezrobocie w województwach i powiatach, próg wejścia (ile trzeba
> zarabiać na kredyt na mieszkanie), limity podatku od nieruchomości, kalendarz odczytów makro. Bez klucza i rejestracji. Opis po polsku: [finwire.pl/otwarte-api](https://finwire.pl/otwarte-api).

---

## Contents

- [Data coverage](#data-coverage)
- [Use cases](#use-cases)
- [Quick start](#quick-start)
- [Response format and citation](#response-format-and-citation)
- [Endpoints](#endpoints)
- [FAQ](#faq)
- [Glossary](#glossary)
- [OpenAPI specification and TypeScript types](#openapi-specification-and-typescript-types)
- [Rate limits](#rate-limits)
- [Usage policy and disclaimer](#usage-policy-and-disclaimer)

---

## Data coverage

Every dataset comes from its primary source and is served in one format, with that source in
`meta`.

Each dataset name links to its documentation page with fields, source and citation.

| Data | Endpoint | Primary source | Frequency | History |
|---|---|---|---|---|
| [NBP policy rates](https://public-api.finwire.pl/datasets/interest-rates) (reference, lombard, deposit, rediscount, discount) | `/v1/series/interest-rate/{id}` | [NBP](https://nbp.pl) | on MPC decisions | up to 10 years |
| [CPI inflation](https://public-api.finwire.pl/datasets/cpi) (YoY, MoM) | `/v1/series/cpi` | [GUS](https://stat.gov.pl) | monthly | since 1982 |
| [NBP exchange rates](https://public-api.finwire.pl/datasets/fx) (table A mid, table C bid/ask) | `/v1/series/fx/{code}` | [NBP](https://nbp.pl/statystyka-i-sprawozdawczosc/kursy) | daily (business days) | up to 10 years |
| [10-year government bond yields](https://public-api.finwire.pl/datasets/bond-yields): PL, DE, euro area, EU + spreads | `/v1/series/bond-yields/{geo}` | [Eurostat irt_lt_mcby_m](https://ec.europa.eu/eurostat/databrowser/view/irt_lt_mcby_m/default/table) | monthly | since 2001 |
| [Retail government bonds](https://public-api.finwire.pl/datasets/bonds) (OTS, ROR, DOR, TOS, COI, EDO, ROS, ROD) | `/v1/bonds` | [Ministry of Finance](https://www.obligacjeskarbowe.pl) | monthly issues | current issues |
| [Flat transaction prices per m²](https://public-api.finwire.pl/datasets/transaction-prices) (median, quartiles, primary/secondary) - 17 cities and their districts | `/v1/housing/transactions/{city}` | Real Estate Price Register (RCN, [GUGiK](https://www.geoportal.gov.pl) + city registers) | quarterly, ingested daily | last 12 quarters per city |
| [Housing prices per m²](https://public-api.finwire.pl/datasets/housing) in 17 cities | `/v1/series/housing/{city}` | [GUS](https://stat.gov.pl) | quarterly | latest quarters |
| [Housing supply](https://public-api.finwire.pl/datasets/housing-supply): dwellings per 1000, completions, permits, migration | `/v1/housing/supply/{city}` | [GUS BDL](https://bdl.stat.gov.pl), Census 2021 | yearly | since 2010 |
| [Asking rents](https://public-api.finwire.pl/datasets/rent) per city and district | `/v1/rent/{city}` | [znajdznajem.pl](https://znajdznajem.pl/metodologia) | daily snapshot | current + previous month |
| [Rental yield](https://public-api.finwire.pl/datasets/rental-yield) (gross, net, payback) | `/v1/rent/yield` | finwire.pl (rents + RCN prices) | daily | current |
| [Wages](https://public-api.finwire.pl/datasets/wages): median, percentiles, 16 provinces | `/v1/wages` | [GUS](https://stat.gov.pl) | on GUS release | current |
| [Wages by occupation](https://public-api.finwire.pl/datasets/wages-by-occupation): mean, median, deciles, gender gap - about 175 groups | `/v1/wages/occupations/{code}` | [GUS](https://stat.gov.pl) | every 2 years | survey editions |
| [Registered unemployment](https://public-api.finwire.pl/datasets/unemployment): Poland, 16 provinces, highest/lowest counties | `/v1/series/unemployment` | [GUS BDL](https://bdl.stat.gov.pl) | monthly | since 2011 |
| [Macroeconomic indicators](https://public-api.finwire.pl/datasets/macro-indicators): GDP, retail sales, output, CPI, unemployment, wages, FX, credit, limits | `/v1/macro/indicators` | [GUS](https://stat.gov.pl), [NBP](https://nbp.pl), [Eurostat](https://ec.europa.eu/eurostat), Ministry of Finance | on release | full history per indicator |
| [Consumer sentiment](https://public-api.finwire.pl/datasets/consumer-sentiment): BWUK, WWUK and 13 components | `/v1/macro/consumer-sentiment` | [GUS](https://dbw.stat.gov.pl) | monthly | since 2010 |
| [Average wage, enterprise sector](https://public-api.finwire.pl/datasets/wages-enterprise) by province | `/v1/wages/enterprise` | [GUS](https://dbw.stat.gov.pl) | monthly | 2 years |
| [Gold price](https://public-api.finwire.pl/datasets/gold) (1 g, fineness 1000) | `/v1/series/gold` | [NBP](https://nbp.pl/statystyka-i-sprawozdawczosc/kursy/cena-zlota/) | daily (business days) | since 2013 |
| [Maximum fuel prices](https://public-api.finwire.pl/datasets/fuel-max-prices): Pb95, Pb98, diesel | `/v1/fuel-prices` | Minister of Energy ([Monitor Polski](https://eli.gov.pl)) | on announcement | all announcements |
| [Legal changes radar](https://public-api.finwire.pl/datasets/legal-changes): acts entering into force, bills in the Sejm | `/v1/legal-changes` | [Sejm ELI API](https://api.sejm.gov.pl/eli.html) | daily | upcoming and recent |
| [Residents income by municipality and county](https://public-api.finwire.pl/datasets/municipal-income) | `/v1/income/municipalities` | Ministry of Finance (Rb-27S), [GUS BDL](https://bdl.stat.gov.pl), computed by finwire.pl | yearly, quarterly update | budget years |
| [Housing running costs](https://public-api.finwire.pl/datasets/housing-upkeep) in 20 cities | `/v1/housing/upkeep/{city}` | [GUS BDL](https://bdl.stat.gov.pl) | yearly | latest year |
| [House prices and housing conditions in Europe](https://public-api.finwire.pl/datasets/eu-housing) | `/v1/housing/europe` | [Eurostat](https://ec.europa.eu/eurostat) | quarterly, yearly | since 2010 (conditions) |
| [Bank lending survey](https://public-api.finwire.pl/datasets/credit-survey) | `/v1/credit-market/survey` | [NBP](https://nbp.pl) | quarterly | editions |
| [Household loans and deposits](https://public-api.finwire.pl/datasets/nbp-monetary) | `/v1/credit-market/monetary` | [NBP](https://nbp.pl) | monthly | up to 400 months |
| [Interest rates on new loans in Europe](https://public-api.finwire.pl/datasets/ecb-mortgage-rates) | `/v1/credit-market/ecb-rates` | [ECB MIR](https://data.ecb.europa.eu) | monthly | since 2014 |
| [Economic calendar](https://public-api.finwire.pl/datasets/economic-calendar): MPC (RPP) decisions, CPI, wages, GDP, unemployment | `/v1/calendar` | GUS, NBP, compiled by finwire.pl | continuous | upcoming events |
| [IKE / IKZE contribution limits](https://public-api.finwire.pl/datasets/legal-limits) | `/v1/legal-limits` | Ministry of Family announcements | yearly | current year |
| [Property tax upper limits](https://public-api.finwire.pl/datasets/local-tax) | `/v1/legal-limits/local-tax` | Minister of Finance ([Monitor Polski](https://eli.gov.pl)) | yearly | announced tax years |
| [Deposit](https://public-api.finwire.pl/datasets/deposit-rates), [savings](https://public-api.finwire.pl/datasets/savings-rates), [mortgage](https://public-api.finwire.pl/datasets/mortgage-rates) and [cash loan](https://public-api.finwire.pl/datasets/cash-loan-rates) rate aggregates | `/v1/market/*` | finwire.pl (aggregate of offers on the market) | daily | current |
| [Entry threshold](https://public-api.finwire.pl/datasets/entry-threshold): wage needed for a mortgage on a model flat, 17 cities | `/v1/index/entry-threshold` | finwire.pl (GUS prices, mortgage offers, GUS wage distribution) | daily | since September 2026 |
| finwire indicators: [credit stress map](https://public-api.finwire.pl/datasets/credit-stress), barometers | `/v1/index/*` | finwire.pl (computed from the above) | daily (credit stress), monthly (barometers) | barometers since 2015 |

Aggregates never expose single offers, deeds or listings - only medians, percentiles and counts.

---

## Use cases

- **Mortgage and savings calculators** - NBP reference rate and market median mortgage rates in one call (`/v1/snapshot`).
- **Newsroom and research charts** - CPI since 1982, unemployment since 2011, bond yields since 2001, with a ready citation in `meta`.
- **Real estate analysis** - transaction prices per district, rents, rental yield and housing supply for 17 cities.
- **Labour market and HR** - wages by occupation with deciles and the gender pay gap, unemployment by county.
- **Personal finance apps** - IKE/IKZE limits, retail bond rates, deposit and savings rate medians.
- **AI assistants and agents** - an [MCP server](https://public-api.finwire.pl/mcp) (Streamable HTTP, no key) serves live macro indicators and the release calendar to Claude, ChatGPT and other MCP clients; [llms.txt](https://public-api.finwire.pl/llms.txt) and the OpenAPI spec describe every dataset in machine-readable form.

---

## Quick start

**curl**

```bash
# Current table of NBP policy rates
curl "https://public-api.finwire.pl/v1/series/interest-rate"

# Median flat transaction price per m² by district in Kraków
curl "https://public-api.finwire.pl/v1/housing/transactions/krakow"

# Next 10 macro releases and MPC decisions
curl "https://public-api.finwire.pl/v1/calendar?limit=10"
```

**JavaScript / TypeScript** (browser or Node 18+)

```ts
const res = await fetch('https://public-api.finwire.pl/v1/series/interest-rate/stopa-referencyjna?days=365');
const { data, meta } = await res.json();
console.log(data.points.at(-1), meta.citation);
```

**Python**

```python
import requests

r = requests.get("https://public-api.finwire.pl/v1/series/cpi", params={"type": "monthly", "limit": 24})
payload = r.json()
for row in payload["data"]["entries"]:
    print(row["period"], row["yoy"])
print(payload["meta"]["citation"])
```

---

## Response format and citation

Every response uses the same envelope. Values below are illustrative - call the endpoint for current data.

```json
{
  "data": { "...": "dataset-specific payload" },
  "meta": {
    "datasetId": "interest-rates",
    "source": "NBP, GPW Benchmark",
    "sourceUrl": "https://nbp.pl",
    "license": "Public NBP / GPW Benchmark data - attribution required.",
    "asOf": "2026-10-14",
    "fetchedAt": "2026-10-15T08:00:00.000Z",
    "citation": "Source: finwire.pl (NBP, GPW Benchmark), https://public-api.finwire.pl/datasets/interest-rates",
    "docsUrl": "https://public-api.finwire.pl/datasets/interest-rates"
  }
}
```

- `meta.asOf` - the date of the data at the source (not the time of your request).
- `meta.citation` - a ready-to-use attribution string with a link. Use it as is.

**How to cite:** `Source: finwire.pl (<primary source>), https://public-api.finwire.pl/datasets/<dataset-id>`

| Code | Meaning |
|---|---|
| `200` | OK |
| `404` | Unknown dataset, indicator, city or `geo` code |
| `429` | Rate limit exceeded (see `Retry-After`) |
| `503` | Primary source temporarily unavailable |

---

## Endpoints

### Interest rates, inflation, FX, bond yields, unemployment

| Endpoint | Returns |
|---|---|
| `GET /v1/series/interest-rate` | Current table of NBP policy rates |
| `GET /v1/series/interest-rate/{id}?days=365` | Daily history. `id`: `stopa-referencyjna`, `stopa-lombardowa`, `stopa-depozytowa`, `stopa-redyskontowa`, `stopa-dyskontowa`. `days`: 7-3650 |
| `GET /v1/series/cpi?type=monthly&limit=24` | CPI YoY and MoM. `type`: `monthly` or `yearly`, `limit`: 1-600 |
| `GET /v1/series/fx` | Current NBP FX table |
| `GET /v1/series/fx/{code}?days=365` | FX history, e.g. `EUR`, `USD`, `CHF`, `GBP` |
| `GET /v1/series/bond-yields` | Latest 10-year yields for `PL`, `DE`, `EA`, `EU27_2020`, PL-DE and PL-EA spreads in bps |
| `GET /v1/series/bond-yields/{geo}?since=2015-01` | Monthly history for one `geo` |
| `GET /v1/series/unemployment` | Registered unemployment rate: Poland and 16 provinces, latest month, YoY change in pp |
| `GET /v1/series/unemployment/counties?limit=10` | Counties (powiaty) with the lowest and highest rate, `limit` 1-50 |
| `GET /v1/series/unemployment/history?unit={unitId}` | Monthly history since 2011 for Poland (default) or a province (`unitId` from `regions[]`) |

### Real estate

| Endpoint | Returns |
|---|---|
| `GET /v1/housing/transactions` | 17 cities: median, p25, p75 price per m² from notarial deeds, primary vs secondary market, transaction count, YoY, quarterly trend |
| `GET /v1/housing/transactions/{city}` | Districts of a city: 12-month median price per m², transaction count, YoY, difference vs city median |
| `GET /v1/series/housing` and `/{city}` | GUS housing prices per m² for 17 cities, latest quarters, nominal and real YoY |
| `GET /v1/rent` and `/{city}` | Asking rents: median, mean, per m², month-over-month change, districts |
| `GET /v1/rent/yield` | Gross and net rental yield and payback period per city |
| `GET /v1/housing/supply` | Dwellings per 1000 residents, stock, completions, permits, floor area per person, population and migration: Poland, 16 provinces, 17 cities, yearly since 2010, Census 2021, GUS population forecast |
| `GET /v1/housing/supply/{city}` | One city vs its province and Poland, rank among 17 cities, yearly history |

`provisionalQuarters` in transaction data lists quarters the registry is still filling in - treat them as preliminary.

### Wages, legal limits, calendar, market aggregates

| Endpoint | Returns |
|---|---|
| `GET /v1/bonds` | Retail government bond rates by issue |
| `GET /v1/wages` | GUS wage median, percentiles and provincial averages |
| `GET /v1/wages/occupations` | Gross wages for about 175 occupation groups: mean, median, deciles D1-D9, men vs women, public vs private, gender pay gap |
| `GET /v1/wages/occupations/{code}` | One occupation group with its parent groups and survey edition history |
| `GET /v1/legal-limits` | IKE / IKZE annual contribution limits and related values |
| `GET /v1/legal-limits/local-tax` | Upper limits of property tax rates (land, buildings, structures) per tax year, with the act and legal basis |
| `GET /v1/macro/indicators` | Headline indicators with the latest reading, change, sparkline and source |
| `GET /v1/macro/indicators/{key}/history?from=2020-01` | Full history of one indicator |
| `GET /v1/macro/consumer-sentiment` | GUS BWUK, WWUK and components with the history |
| `GET /v1/wages/enterprise` | GUS average wage in the enterprise sector, real change, by province |
| `GET /v1/series/gold` | NBP gold price: latest, period changes, all-time high, 12-month average |
| `GET /v1/series/gold/history?range=1y` | Gold price history, `range`: `1m`, `1y`, `5y`, `max` |
| `GET /v1/fuel-prices` | Maximum fuel prices: in force today, announced for tomorrow, history |
| `GET /v1/legal-changes?days=90` | Acts entering into force soon and recently, open bills in the Sejm |
| `GET /v1/legal-changes/process/{term}/{number}` | One bill: last stage, passage, published act |
| `GET /v1/income/municipalities?year=2025` | Residents income per capita by municipality: rank, ratio to the national average, change |
| `GET /v1/income/counties?year=2025` | The same by county |
| `GET /v1/housing/upkeep/{city}` | Monthly running costs of a flat in a city (list: `/v1/housing/upkeep`) |
| `GET /v1/housing/europe` | Eurostat house price index: Poland vs EU vs euro area and country ranking |
| `GET /v1/housing/europe/overburden` and `/overcrowding` | Housing cost overburden and overcrowding rates by country |
| `GET /v1/credit-market/survey?quarters=12` | NBP bank lending survey |
| `GET /v1/credit-market/monetary?months=25` | NBP household loans, deposits and new loans |
| `GET /v1/credit-market/ecb-rates?product=mortgage_total&period=2026-08` | ECB MIR rate on new loans by country |
| `GET /v1/credit-market/ecb-rates/series?areas=PL,U2,DE&from=2018-01` | ECB MIR history for selected countries |
| `GET /v1/calendar?limit=10` | Upcoming releases and decisions with date, Warsaw time, category, importance 1-10 |
| `GET /v1/market/deposit-rates?amount=10000&period=3` | Term deposit rates: median, percentiles, spread |
| `GET /v1/market/savings-rates?amount=10000` | Savings account rates: median, percentiles |
| `GET /v1/market/mortgage-rates?amount=400000&period=25` | Mortgage APRC (RRSO), margin, nominal rate: median, percentiles |
| `GET /v1/market/cash-loan-rates?amount=20000&period=48` | Cash loan APRC and nominal rate: median, percentiles |

### finwire indicators

| Endpoint | Returns |
|---|---|
| `GET /v1/index/entry-threshold` | Gross monthly wage per person needed for a mortgage on a model flat (parameters in `params`) within KNF DSTI limits in 17 cities, the installment, and the share of employees earning at least that |
| `GET /v1/index/entry-threshold/history?household=single` | Daily change points per city, `household`: `single` or `couple` |
| `GET /v1/index/credit-stress` | Mortgage installment for a 50 m² flat as % of the average net wage in 16 provinces, with KNF Recommendation S DSTI thresholds |
| `GET /v1/index` | All barometers with value, position against history and monthly change |
| `GET /v1/index/{slug}` | One barometer with monthly series, methodology and best/worst month since 2015. `slug`: `real-deposit-rate`, `housing-affordability`, `credit-cost`, `borrowing-power`, `purchasing-power` |
| `GET /v1/snapshot` | Rates, inflation, FX, credit stress and barometers in one call |

Full field-level schemas: [openapi.json](https://public-api.finwire.pl/openapi.json).

---

## FAQ

### Does the API return WIBOR or POLSTR?

No. WIBOR and POLSTR are administered by GPW Benchmark, and redistributing them requires a
Market Data License Agreement with GPW (dystrybucja.informacji@gpw.pl). The API returns NBP
policy rates: current values at `/v1/series/interest-rate` and daily history at
`/v1/series/interest-rate/{id}`.

### Where can I get Polish CPI inflation data as JSON?

`/v1/series/cpi` returns GUS consumer price inflation year-over-year and month-over-month,
monthly since 1982 (`type=monthly`) or annually (`type=yearly`), with the GUS source in
`meta`.

### Is there an API for real estate transaction prices in Poland?

`/v1/housing/transactions` returns median, quartile and per-district flat prices per m²
computed from notarial deeds in the Polish Real Estate Price Register (Rejestr Cen
Nieruchomości) for 17 cities, with a quarterly trend for the last 12 quarters and a
12-month median per district. Single deeds are never exposed.

### How much do I need to earn to buy a flat in a Polish city?

`/v1/index/entry-threshold` returns, for 17 Polish cities, the gross monthly wage one person
needs to get a mortgage on a model 50 m² secondary-market flat (20% down payment, 25 years)
within the KNF DSTI limits, the monthly installment and the share of employees who earn at
least that. It is recomputed every night from GUS prices, current mortgage offers and the GUS
wage distribution; `/history` shows how it changed.

### Where can I get unemployment data by county (powiat)?

`/v1/series/unemployment` returns the GUS registered unemployment rate for Poland and the 16
provinces, `/v1/series/unemployment/counties` the counties with the lowest and highest rate,
and `/v1/series/unemployment/history` the monthly series since 2011, all from the GUS Local
Data Bank (BDL).

### Is there an API for salaries by occupation in Poland?

`/v1/wages/occupations` returns mean, median and deciles of gross monthly wages for about 175
occupation groups from the GUS survey of wages by occupation (published every two years),
split by gender and by public vs private sector, with the gender pay gap.

### Do I need an API key or an account?

No key, token or sign-up is required. All endpoints are public, read-only (GET) and
CORS-enabled, so they can be called directly from a browser.

### Can I use the data commercially?

finwire indicators and aggregates are licensed under CC BY 4.0, which allows commercial use
with attribution. Public data (NBP, GUS, Ministry of Finance, Eurostat, GPW Benchmark)
requires attribution to the original source; `meta.license` states the terms per dataset.

### How fresh is the data?

Each dataset is refreshed automatically from its primary source on the source's own schedule
(daily for FX and rents, monthly for CPI and bond yields, quarterly for housing prices).
`meta.asOf` always shows the date of the data at the source.

### What is the difference between WIBOR and POLSTR?

WIBOR and POLSTR are both Polish interest rate benchmarks administered by GPW Benchmark. In
the ongoing Polish benchmark reform supervised by KNF, POLSTR is the designated successor to
WIBOR. Neither is available in this API (license required from GPW Benchmark).

### Does the API give investment advice?

No. The data is informational. finwire.pl is not a licensed investment adviser and does not
issue recommendations within the meaning of the EU Market Abuse Regulation (MAR).

---

## Glossary

- **NBP reference rate** - the main policy rate of Narodowy Bank Polski, set by the Monetary Policy Council (RPP).
- **WIBOR** (Warsaw Interbank Offered Rate) - Polish interbank benchmark used for floating-rate loans.
- **POLSTR** (Polish Short Term Rate) - GPW Benchmark interest rate index, WIBOR's successor in the benchmark reform.
- **CPI** - consumer price index published monthly by GUS (Statistics Poland).
- **RCN** (Rejestr Cen Nieruchomości) - Real Estate Price Register with prices from notarial deeds.
- **RRSO** - Polish APRC (annual percentage rate of charge), the total cost of a loan.
- **DSTI** - debt service to income ratio, the share of income spent on loan installments.
- **bps** - basis points, 1 bps = 0.01 percentage point.
- **BDL** (Bank Danych Lokalnych) - GUS Local Data Bank with statistics for provinces, counties and municipalities.
- **TERYT** - official Polish territorial code of provinces, counties and municipalities.
- **Entry threshold** (próg wejścia) - finwire indicator: the wage needed to qualify for a mortgage on a model flat.
- **Property tax upper limit** - the maximum local property tax rate set yearly by the Minister of Finance; municipalities choose their rate up to it.

---

## OpenAPI specification and TypeScript types

This repository ships the OpenAPI specification and TypeScript types generated from it.
Both are regenerated from the live API on every release.

- [`openapi.json`](openapi.json) - OpenAPI 3 specification (live: [public-api.finwire.pl/openapi.json](https://public-api.finwire.pl/openapi.json))
- [`types/api.ts`](types/api.ts) - TypeScript types generated with [openapi-typescript](https://github.com/openapi-ts/openapi-typescript)

```ts
import type { paths } from './types/api';

type TransactionPrices =
  paths['/v1/housing/transactions/{city}']['get']['responses']['200']['content']['application/json'];
```

---

## Rate limits

- 120 requests per minute per IP, 30 per minute for `/v1/snapshot`.
- Headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`; `429` comes with `Retry-After`.
- Responses are cached at the edge (5 minutes to 1 hour depending on the dataset), so typical use never hits the limit.

---

## Usage policy and disclaimer

- Free and open, GET only. Please cache responses on your side.
- Attribute the data with the `meta.citation` string or `Source: finwire.pl, https://public-api.finwire.pl`.
- Single bank offers, notarial deeds and rental listings are never exposed - only statistics.
- Informational data only, not investment advice (see [FAQ](#does-the-api-give-investment-advice)).

Built by [finwire.pl](https://finwire.pl) - Polish personal finance tools: [mortgage decision tool](https://finwire.pl/decyzjomat/hipoteka), [flat prices by city](https://finwire.pl/ceny-mieszkan), [deposit rankings](https://finwire.pl/rankingi/lokaty).

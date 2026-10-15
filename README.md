# finwire API - free Polish financial data API (WIBOR, POLSTR, NBP, GUS, RCN)

**finwire API is a free, open REST API with financial and real estate data for Poland:
NBP policy rates, WIBOR and POLSTR, CPI inflation, NBP exchange rates, 10-year government
bond yields, flat transaction prices from notarial deeds, rents, wages and an economic
calendar.** No API key, JSON over HTTPS, OpenAPI 3 specification, every response with its
primary source and a ready-to-paste citation. Built and maintained by [finwire.pl](https://finwire.pl).

[![Docs](https://img.shields.io/badge/docs-public--api.finwire.pl-3b82f6)](https://public-api.finwire.pl)
[![OpenAPI](https://img.shields.io/badge/OpenAPI-3.0-22c55e)](https://public-api.finwire.pl/openapi.json)
[![Data license](https://img.shields.io/badge/data-CC%20BY%204.0-orange)](https://creativecommons.org/licenses/by/4.0/)

```bash
curl "https://public-api.finwire.pl/v1/series/interest-rate/wibor-3m?days=90"
```

- **Base URL:** `https://public-api.finwire.pl`
- **Auth:** none - no key, no sign-up, GET only, CORS enabled (works from the browser)
- **Format:** JSON, UTF-8, `{ data, meta }` envelope
- **Specification:** [openapi.json](https://public-api.finwire.pl/openapi.json) · [interactive reference](https://public-api.finwire.pl/reference) · [llms.txt](https://public-api.finwire.pl/llms.txt) · [dataset pages](https://public-api.finwire.pl/datasets)
- **Data license:** CC BY 4.0 for finwire indicators and aggregates; public data requires attribution to the original source
- **Changes:** [CHANGELOG.md](CHANGELOG.md)

> **Po polsku:** darmowe API z polskimi danymi finansowymi - stopy NBP, WIBOR i POLSTR,
> inflacja CPI (GUS), kursy walut NBP, rentowności obligacji, ceny transakcyjne mieszkań
> z Rejestru Cen Nieruchomości, czynsze i rentowność najmu, wynagrodzenia GUS, kalendarz
> odczytów makro. Bez klucza i rejestracji. Opis po polsku: [finwire.pl/dla-developerow](https://finwire.pl/dla-developerow).

---

## Contents

- [Data coverage](#data-coverage)
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

| Data | Endpoint | Primary source | Frequency | History |
|---|---|---|---|---|
| NBP policy rates (reference, lombard, deposit, rediscount, discount) | `/v1/series/interest-rate/{id}` | [NBP](https://nbp.pl) | on MPC decisions | up to 10 years |
| WIBOR 1M, 3M, 6M, 1Y | `/v1/series/interest-rate/{id}` | [GPW Benchmark](https://gpwbenchmark.pl) | daily (business days) | up to 10 years |
| POLSTR ON, 1M, 3M, 6M | `/v1/series/interest-rate/{id}` | [GPW Benchmark](https://gpwbenchmark.pl) | daily (business days) | since ingestion start |
| CPI inflation (YoY, MoM) | `/v1/series/cpi` | [GUS](https://stat.gov.pl) | monthly | since 1982 |
| NBP exchange rates (table A mid, table C bid/ask) | `/v1/series/fx/{code}` | [NBP](https://nbp.pl/statystyka-i-sprawozdawczosc/kursy) | daily (business days) | since 2021 |
| 10-year government bond yields: PL, DE, euro area, EU + spreads | `/v1/series/bond-yields/{geo}` | [Eurostat irt_lt_mcby_m](https://ec.europa.eu/eurostat/databrowser/view/irt_lt_mcby_m/default/table) | monthly | since 2001 |
| Retail government bonds (OTS, ROR, DOR, TOS, COI, EDO, ROS, ROD) | `/v1/bonds` | [Ministry of Finance](https://www.obligacjeskarbowe.pl) | monthly issues | current issues |
| Flat transaction prices per m² (median, quartiles, primary/secondary) - 17 cities and their districts | `/v1/housing/transactions/{city}` | Real Estate Price Register (RCN, [GUGiK](https://www.geoportal.gov.pl) + city registers) | quarterly, ingested daily | last 12 quarters per city |
| Housing prices per m² in 17 cities | `/v1/series/housing/{city}` | [GUS](https://stat.gov.pl) | quarterly | latest quarters |
| Asking rents per city and district | `/v1/rent/{city}` | [znajdznajem.pl](https://znajdznajem.pl/metodologia) | daily snapshot | current + previous month |
| Rental yield (gross, net, payback) | `/v1/rent/yield` | finwire.pl (rents + RCN prices) | daily | current |
| Wages: median, percentiles, 16 provinces | `/v1/wages` | [GUS](https://stat.gov.pl) | on GUS release | current |
| Economic calendar: MPC (RPP) decisions, CPI, wages, GDP, unemployment | `/v1/calendar` | GUS, NBP, compiled by finwire.pl | continuous | upcoming events |
| IKE / IKZE contribution limits | `/v1/legal-limits` | Ministry of Family announcements | yearly | current year |
| Deposit, savings, mortgage and cash loan rate aggregates | `/v1/market/*` | finwire.pl (aggregate of offers on the market) | daily | current |
| finwire indicators: credit stress map, barometers | `/v1/index/*` | finwire.pl (computed from the above) | daily (credit stress), monthly (barometers) | barometers since 2015 |

Aggregates never expose single offers, deeds or listings - only medians, percentiles and counts.

---

## Quick start

**curl**

```bash
# Current table of all NBP, WIBOR and POLSTR rates
curl "https://public-api.finwire.pl/v1/series/interest-rate"

# Median flat transaction price per m² by district in Kraków
curl "https://public-api.finwire.pl/v1/housing/transactions/krakow"

# Next 10 macro releases and MPC decisions
curl "https://public-api.finwire.pl/v1/calendar?limit=10"
```

**JavaScript / TypeScript** (browser or Node 18+)

```ts
const res = await fetch('https://public-api.finwire.pl/v1/series/interest-rate/wibor-3m?days=365');
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

### Interest rates, inflation, FX, bond yields

| Endpoint | Returns |
|---|---|
| `GET /v1/series/interest-rate` | Current table: NBP rates, WIBOR, POLSTR (all tenors) |
| `GET /v1/series/interest-rate/{id}?days=365` | Daily history. `id`: `stopa-referencyjna`, `stopa-lombardowa`, `stopa-depozytowa`, `stopa-redyskontowa`, `stopa-dyskontowa`, `wibor-1m`, `wibor-3m`, `wibor-6m`, `wibor-1y`, `polstr-on`, `polstr-1m`, `polstr-3m`, `polstr-6m`. `days`: 7-3650 |
| `GET /v1/series/cpi?type=monthly&limit=24` | CPI YoY and MoM. `type`: `monthly` or `yearly`, `limit`: 1-600 |
| `GET /v1/series/fx` | Current NBP FX table |
| `GET /v1/series/fx/{code}?days=365` | FX history, e.g. `EUR`, `USD`, `CHF`, `GBP` |
| `GET /v1/series/bond-yields` | Latest 10-year yields for `PL`, `DE`, `EA`, `EU27_2020`, PL-DE and PL-EA spreads in bps |
| `GET /v1/series/bond-yields/{geo}?since=2015-01` | Monthly history for one `geo` |

### Real estate

| Endpoint | Returns |
|---|---|
| `GET /v1/housing/transactions` | 17 cities: median, p25, p75 price per m² from notarial deeds, primary vs secondary market, transaction count, YoY, quarterly trend |
| `GET /v1/housing/transactions/{city}` | Districts of a city: 12-month median price per m², transaction count, YoY, difference vs city median |
| `GET /v1/series/housing` and `/{city}` | GUS housing prices per m² for 17 cities, latest quarters, nominal and real YoY |
| `GET /v1/rent` and `/{city}` | Asking rents: median, mean, per m², month-over-month change, districts |
| `GET /v1/rent/yield` | Gross and net rental yield and payback period per city |

`provisionalQuarters` in transaction data lists quarters the registry is still filling in - treat them as preliminary.

### Reference data, calendar, market aggregates

| Endpoint | Returns |
|---|---|
| `GET /v1/bonds` | Retail government bond rates by issue |
| `GET /v1/wages` | GUS wage median, percentiles and provincial averages |
| `GET /v1/legal-limits` | IKE / IKZE annual contribution limits and related values |
| `GET /v1/calendar?limit=10` | Upcoming releases and decisions with date, Warsaw time, category, importance 1-10 |
| `GET /v1/market/deposit-rates?amount=10000&period=3` | Term deposit rates: median, percentiles, spread |
| `GET /v1/market/savings-rates?amount=10000` | Savings account rates: median, percentiles |
| `GET /v1/market/mortgage-rates?amount=400000&period=25` | Mortgage APRC (RRSO), margin, nominal rate: median, percentiles |
| `GET /v1/market/cash-loan-rates?amount=20000&period=48` | Cash loan APRC and nominal rate: median, percentiles |

### finwire indicators

| Endpoint | Returns |
|---|---|
| `GET /v1/index/credit-stress` | Mortgage installment for a 50 m² flat as % of the average net wage in 16 provinces, with KNF Recommendation S DSTI thresholds |
| `GET /v1/index` | All barometers with value, position against history and monthly change |
| `GET /v1/index/{slug}` | One barometer with monthly series, methodology and best/worst month since 2015. `slug`: `real-deposit-rate`, `housing-affordability`, `credit-cost`, `borrowing-power`, `purchasing-power` |
| `GET /v1/snapshot` | Rates, inflation, FX, credit stress and barometers in one call |

Full field-level schemas: [openapi.json](https://public-api.finwire.pl/openapi.json).

---

## FAQ

### Is there a free API for WIBOR and POLSTR rates?

finwire API returns WIBOR (1M, 3M, 6M, 1Y) and POLSTR (ON, 1M, 3M, 6M) published by GPW
Benchmark, together with NBP policy rates, for free and without an API key: current values
at `/v1/series/interest-rate` and daily history at `/v1/series/interest-rate/{id}`.

### Where can I get Polish CPI inflation data as JSON?

`/v1/series/cpi` returns GUS consumer price inflation year-over-year and month-over-month,
monthly since 1982 (`type=monthly`) or annually (`type=yearly`), with the GUS source in
`meta`.

### Is there an API for real estate transaction prices in Poland?

`/v1/housing/transactions` returns median, quartile and per-district flat prices per m²
computed from notarial deeds in the Polish Real Estate Price Register (Rejestr Cen
Nieruchomości) for 17 cities, with a quarterly trend for the last 12 quarters and a
12-month median per district. Single deeds are never exposed.

### Do I need an API key or an account?

No key, token or sign-up is required. All endpoints are public, read-only (GET) and
CORS-enabled, so they can be called directly from a browser.

### Can I use the data commercially?

finwire indicators and aggregates are licensed under CC BY 4.0, which allows commercial use
with attribution. Public data (NBP, GUS, Ministry of Finance, Eurostat, GPW Benchmark)
requires attribution to the original source; `meta.license` states the terms per dataset.

### How fresh is the data?

Each dataset is refreshed automatically from its primary source on the source's own schedule
(daily for rates, FX and rents, monthly for CPI and bond yields, quarterly for housing prices).
`meta.asOf` always shows the date of the data at the source.

### What is the difference between WIBOR and POLSTR?

WIBOR and POLSTR are both Polish interest rate benchmarks administered by GPW Benchmark. In
the ongoing Polish benchmark reform supervised by KNF, POLSTR is the designated successor to
WIBOR. Both are available in this API.

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

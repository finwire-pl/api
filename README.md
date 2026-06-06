# finwire API - Public financial data API

> Open, normalized financial data for Poland + finwire's own indicators, by **[finwire.pl](https://finwire.pl)**.

[![Docs](https://img.shields.io/badge/docs-public--api.finwire.pl-3b82f6)](https://public-api.finwire.pl)
[![OpenAPI](https://img.shields.io/badge/OpenAPI-/reference-22c55e)](https://public-api.finwire.pl/reference)
[![License](https://img.shields.io/badge/data-CC%20BY%204.0-orange)](https://creativecommons.org/licenses/by/4.0/)

One API for Polish financial data: NBP policy rates, WIBOR/POLSTR, CPI inflation, NBP FX
rates, NBP BaRN housing prices, GUS wages, government bonds, plus indicators computed by
finwire (credit stress map, barometers). Everything in one clean JSON format, with explicit
source attribution and a ready-to-use citation in every response.

- **Base URL:** `https://public-api.finwire.pl`
- **Auth:** none - the API is open (GET only)
- **Format:** JSON, UTF-8
- **Interactive docs:** [/reference](https://public-api.finwire.pl/reference) · [openapi.json](https://public-api.finwire.pl/openapi.json) · [llms.txt](https://public-api.finwire.pl/llms.txt)

---

## Table of contents

- [Response format](#response-format)
- [Rate limits](#rate-limits)
- [Endpoints](#endpoints)
  - [Time series](#time-series)
  - [finwire indicators](#finwire-indicators)
  - [Market aggregates](#market-aggregates)
  - [Snapshot](#snapshot)
- [OpenAPI specification & types](#openapi-specification--types)
- [Citation](#citation)
- [Usage policy](#usage-policy)
- [Disclaimer](#disclaimer)
- [License](#license)

---

## Response format

Every response uses the same `{ data, meta }` envelope:

```json
{
  "data": { "...": "the actual data" },
  "meta": {
    "datasetId": "interest-rates",
    "source": "NBP, GPW Benchmark",
    "sourceUrl": "https://nbp.pl",
    "license": "Public NBP / GPW Benchmark data - attribution required.",
    "asOf": "2026-06-05",
    "fetchedAt": "2026-06-06T08:00:00.000Z",
    "citation": "Source: finwire.pl (NBP, GPW Benchmark), https://public-api.finwire.pl/datasets/interest-rates",
    "docsUrl": "https://public-api.finwire.pl/datasets/interest-rates"
  }
}
```

`meta.citation` is a ready-to-use attribution string with a backlink.

### Errors

| Code | Meaning |
|---|---|
| `200` | OK |
| `404` | Unknown dataset / indicator / city |
| `429` | Rate limit exceeded (see `Retry-After`) |
| `503` | Upstream data source temporarily unavailable |

---

## Rate limits

- **120 requests / minute / IP** for regular reads.
- **30 requests / minute / IP** for heavy endpoints (`/v1/snapshot`).

Every response includes `X-RateLimit-Limit`, `X-RateLimit-Remaining` and `X-RateLimit-Reset`
headers. When the limit is exceeded a `429` is returned with a `Retry-After` header. Data is
cached at the edge (refreshed once a day), so under typical use the limit is unreachable.

---

## Endpoints

| Group | Endpoint | Source |
|---|---|---|
| Series | `GET /v1/series/interest-rate` | NBP, GPW Benchmark |
| Series | `GET /v1/series/interest-rate/{indicator}` | NBP, GPW Benchmark |
| Series | `GET /v1/series/cpi` | GUS |
| Series | `GET /v1/series/fx` | NBP |
| Series | `GET /v1/series/fx/{code}` | NBP |
| Series | `GET /v1/series/housing` | NBP BaRN |
| Series | `GET /v1/series/housing/{city}` | NBP BaRN |
| Reference | `GET /v1/bonds` | Ministry of Finance |
| Reference | `GET /v1/wages` | GUS |
| Reference | `GET /v1/legal-limits` | Ministry of Family |
| Indicators | `GET /v1/index` | finwire |
| Indicators | `GET /v1/index/credit-stress` | finwire (NBP+GUS+WIBOR) |
| Indicators | `GET /v1/index/{slug}` | finwire (barometers) |
| Aggregates | `GET /v1/market/deposit-rates` | finwire (aggregate) |
| Aggregates | `GET /v1/market/savings-rates` | finwire (aggregate) |
| Aggregates | `GET /v1/market/mortgage-rates` | finwire (aggregate) |
| Aggregates | `GET /v1/market/cash-loan-rates` | finwire (aggregate) |
| Snapshot | `GET /v1/snapshot` | aggregate |

---

### Time series

#### `GET /v1/series/interest-rate/{indicator}`

History of a rate indicator. `indicator`: e.g. `wibor-3m`, `wibor-6m`, `wibor-1m`,
`wibor-1y`, `nbp_rate`, `polstr-3m`, `polstr-6m`. Query `days` (1-3650, default 365).
Without `{indicator}` it returns a table of current values for all indicators.

```bash
curl "https://public-api.finwire.pl/v1/series/interest-rate/wibor-3m?days=90"
```

#### `GET /v1/series/cpi`

CPI inflation. Query: `type` (`monthly`|`yearly`, default `monthly`), `limit` (1-600).

```bash
curl "https://public-api.finwire.pl/v1/series/cpi?type=monthly&limit=12"
```

```json
{
  "data": {
    "source": "GUS (stat.gov.pl)",
    "count": 12,
    "entries": [
      { "period": "2026-04", "yoy": 3.2, "mom": null },
      { "period": "2026-03", "yoy": 3.0, "mom": 0.1 }
    ]
  },
  "meta": { "datasetId": "cpi", "source": "GUS", "...": "..." }
}
```

#### `GET /v1/series/fx/{code}`

Currency rate history (`EUR`, `USD`, `CHF`, `GBP`, ...). Query `days` as above.
Without `{code}` it returns the current FX table.

```bash
curl "https://public-api.finwire.pl/v1/series/fx/EUR?days=30"
```

#### `GET /v1/series/housing/{city}`

NBP BaRN housing price history for a provincial capital (`warszawa`, `krakow`,
`gdansk`, `wroclaw`, `poznan`, ...). Without `{city}` it returns the list of cities.

```bash
curl "https://public-api.finwire.pl/v1/series/housing/krakow"
```

#### Reference data

```bash
curl "https://public-api.finwire.pl/v1/bonds"         # government bonds (Ministry of Finance)
curl "https://public-api.finwire.pl/v1/wages"         # median/percentile wages + provinces (GUS)
curl "https://public-api.finwire.pl/v1/legal-limits"  # IKE/IKZE limits
```

---

### finwire indicators

Proprietary indicators computed by finwire on top of public data.

#### `GET /v1/index/credit-stress`

Credit stress map: the installment of a model mortgage for a 50 m2 flat as a percentage of
the average net wage in each of the 16 provinces, with DSTI thresholds per the KNF
Recommendation S.

```bash
curl "https://public-api.finwire.pl/v1/index/credit-stress"
```

```json
{
  "data": {
    "params": { "metrazM2": 50, "okresLat": 25, "ltvPct": 80, "rynek": "wtórny" },
    "rates": { "wibor3m": 3.85, "marginPp": 2, "ratePct": 5.85 },
    "sources": { "barnQuarter": "Q1 2026", "gusPeriod": "Q4 2025" },
    "national": { "avgStressPct": 47.2, "medianStressPct": 43.8 },
    "regions": [ { "slug": "malopolskie", "stressPct": 64.6, "tier": 3 } ]
  }
}
```

#### `GET /v1/index/{slug}`

finwire barometers. Available `slug`:

| `slug` | Description |
|---|---|
| `real-deposit-rate` | Real return on deposits (rate minus inflation) |
| `housing-affordability` | Housing affordability (square meters financeable) |
| `credit-cost` | Mortgage cost |
| `borrowing-power` | Borrowing power |
| `purchasing-power` | Purchasing power (inflation) |

```bash
curl "https://public-api.finwire.pl/v1/index/real-deposit-rate"
```

`GET /v1/index` returns the list of all indicators.

---

### Market aggregates

Interest rate statistics for products currently on the market. **Only aggregates are
returned (median, percentiles, min/max, count) - individual bank offers are never exposed.**

#### `GET /v1/market/{deposit|savings|mortgage|cash-loan}-rates`

Query: `amount`, `period` (deposits/mortgages/cash loans), `currency` (deposits: PLN/EUR/USD).

```bash
curl "https://public-api.finwire.pl/v1/market/mortgage-rates?amount=400000&period=25"
```

```json
{
  "data": {
    "product": "mortgage",
    "model": { "amount": 400000, "period": 25, "ltvPct": 80 },
    "sampleSize": 9,
    "bankCount": 4,
    "note": "Aggregates computed from offers available at the time. Individual offers are not exposed.",
    "rates": {
      "rrso":        { "count": 9, "min": 5.7, "p25": 5.91, "median": 6.08, "p75": 6.14, "avg": 6.31, "max": 8.25 },
      "margin":      { "count": 9, "min": 1.8, "p25": 1.85, "median": 1.87, "p75": 2.0, "avg": 2.18, "max": 3.84 },
      "nominalRate": { "count": 9, "min": 5.56, "p25": 5.61, "median": 5.76, "p75": 5.89, "avg": 5.99, "max": 7.6 }
    }
  }
}
```

`rrso` is the Polish APRC (annual percentage rate of charge).

---

### Snapshot

#### `GET /v1/snapshot`

The current state of the market in a single call: rates, inflation, FX, the credit stress
indicator and barometers. Degrades gracefully - if a source is unavailable its field is
`null` and the rest still works.

```bash
curl "https://public-api.finwire.pl/v1/snapshot"
```

---

## OpenAPI specification & types

This repository ships the generated OpenAPI specification and TypeScript types so you can
import them directly or run your own codegen.

- [`openapi.json`](openapi.json) - the OpenAPI 3 specification (also live at
  [public-api.finwire.pl/openapi.json](https://public-api.finwire.pl/openapi.json))
- [`types/api.ts`](types/api.ts) - TypeScript types generated from the spec with
  [`openapi-typescript`](https://github.com/openapi-ts/openapi-typescript)

```ts
import type { paths } from './types/api';

type CpiResponse =
  paths['/v1/series/cpi']['get']['responses']['200']['content']['application/json'];
```

Regenerate after a spec change:

```bash
curl -s https://public-api.finwire.pl/openapi.json -o openapi.json
npx openapi-typescript ./openapi.json -o ./types/api.ts
```

---

## Citation

You may use the data with attribution and a backlink to finwire.pl:

> Source: finwire.pl, https://public-api.finwire.pl

The `meta.citation` field in every response contains a ready-to-use string. Public data
(NBP, GUS, Ministry of Finance) requires attribution to the original source; finwire
indicators are available under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

---

## Usage policy

- The API is open and free, GET methods only.
- Limit of 120 requests / minute / IP (30 for snapshot).
- Raw bank offers are not exposed - the aggregates section returns statistics only.

---

## Disclaimer

The data is provided for informational purposes only. finwire.pl is not a licensed investment
adviser and does not provide investment recommendations within the meaning of the EU Market
Abuse Regulation (MAR).

---

## License

- **Data:** public data (NBP/GUS/Ministry of Finance) requires source attribution; finwire
  indicators are under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). See the
  `meta.license` field in each response for details.

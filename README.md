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
- [MCP server for AI assistants](#mcp-server-for-ai-assistants)
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
| [Retail prices in shops](https://public-api.finwire.pl/datasets/retail-prices): 30 goods and services, finwire shopping basket, minutes of work | `/v1/prices/retail` | GUS ([Dziedzinowa Baza Wiedzy](https://dbw.stat.gov.pl/)) | monthly | from 2018 |
| [Business registrations](https://public-api.finwire.pl/datasets/business-registrations): new, closed, suspended and resumed sole proprietorships in CEIDG, share of women, `finwire_new_business_ratio` | `/v1/business-registrations` | [MRiT](https://dane.gov.pl/pl/dataset/745) (dane.gov.pl) | monthly | from November 2019 |
| [Vehicle registrations](https://public-api.finwire.pl/datasets/vehicle-registrations): first registrations by vehicle type and province, passenger-car brands, share of brands from China, `finwire_car_registrations_pulse`, `finwire_chinese_brands_share` | `/v1/vehicle-registrations` | [Ministry of Digital Affairs](https://dane.gov.pl/pl/dataset/440) (CEPiK data, dane.gov.pl) | monthly | from January 2014, brands from January 2023 |
| [Electric cars](https://public-api.finwire.pl/datasets/electric-vehicles): BEV and PHEV fleet and new cars, fuel mix of new cars, counties and provinces, charging vs EV correlation, `finwire_ev_share_new_cars` | `/v1/electric-vehicles` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/road_eqs_carpda/default/table), GUS BDL, UDT EIPA | yearly | Eurostat from 2013 |
| [EV charging points](https://public-api.finwire.pl/datasets/ev-charging): locations, stations and points, fast sites by province and county, `finwire_ev_charging_coverage` | `/v1/ev-charging` | [UDT EIPA](https://eipa.udt.gov.pl/stats) | monthly (month ends), daily current state | from December 2019 |
| [IKE and IKZE retirement accounts](https://public-api.finwire.pl/datasets/ike-ikze): accounts, assets and contributions by institution type, limit usage, `finwire_ike_ikze_avg_balance` | `/v1/ike-ikze` | [KNF](https://www.knf.gov.pl/wyniki_wyszukiwania?searchText=Informacje+o+rynku+IKE) | half-yearly | from June 2020 |
| [Investment funds](https://public-api.finwire.pl/datasets/investment-funds): assets and net flows of the market, fund groups, PPK, bond types, equity regions, `finwire_fund_flows_index` | `/v1/investment-funds` | [IZFiA](https://www.izfa.pl/raporty) | monthly | market from February 2020, groups from February 2022 |
| [Czyste Powietrze subsidy programme](https://public-api.finwire.pl/datasets/clean-air): applications, contracts, payouts, payout queue, heat sources, `finwire_clean_air_payout_rate` | `/v1/subsidies/clean-air` | [NFOŚiGW](https://czystepowietrze.gov.pl/efekty-programu/czyste-powietrze-w-liczbach) | weekly | from February 2024 (archived readings) |
| [Average ZUS pension](https://public-api.finwire.pl/datasets/zus-benefits): gross and net, other pensions, allowances, `finwire_pension_wage_ratio` | `/v1/pensions` | [ZUS](https://www.zus.pl/baza-wiedzy/statystyka/miesieczna-informacja-o-wybranych-swiadczeniach-pienieznych), GUS | monthly (preliminary data) | from December 2023 |
| [Average Pole](https://public-api.finwire.pl/datasets/average-pole) (`finwire_average_pole`): median age, median wage, flat price, average mortgage and model payment, pension, spending, car age, top car brand | `/v1/average-pole` | Eurostat, GUS, BIK, NBP, ZUS, Ministry of Digital Affairs | each trait on its source's release (annual to monthly) | latest readings |
| [Daily price index](https://public-api.finwire.pl/datasets/daily-price-index) (`finwire_daily_price_index`, experimental): online shelf prices of a fixed basket of food, household and personal care products, base month = 100, categories, m/m, food part vs GUS CPI food | `/v1/daily-price-index` | finwire.pl from Frisco.pl, Rossmann.pl, zakupy.biedronka.pl; [GUS](https://dbw.stat.gov.pl/) CPI weights |
| [Pension forecast](https://public-api.finwire.pl/datasets/pension-forecast) (`finwire_pension_forecast`): first ZUS pension as % of the last wage and in today's money by birth year, sex and retirement age and wage level, P10/P50/P90, effect of demography and longer life, OECD and EC benchmarks | `/v1/pension-forecast` | [ZUS](https://www.zus.pl/), [Eurostat](https://ec.europa.eu/eurostat), GUS, Ministry of Finance | recomputed when ZUS indexation, Eurostat or GUS inputs change | birth years 1966-2006, retirement years to 2075 |
| [PPK (Employee Capital Plans)](https://public-api.finwire.pl/datasets/ppk): net assets, active accounts, participants, participation rate, average fee, `finwire_ppk_avg_account` | `/v1/ppk` | [PFR Portal PPK](https://www.mojeppk.pl/pliki-do-pobrania.html) (monthly bulletin) | monthly | from October 2021 |
| [Retail savings bonds vs bank deposits](https://public-api.finwire.pl/datasets/retail-bond-premium): first-year bond rates minus best deposits, savings accounts and average new deposit after tax, `finwire_retail_bond_premium` | `/v1/retail-bond-premium` | Ministry of Finance, NBP, finwire.pl deposit indices | monthly | from the first month with both bond issue and deposit data |
| [Household fixed cost burden](https://public-api.finwire.pl/datasets/fixed-cost-burden): mortgage, admin fee, utilities and TV licence as % of net income, Poland and 16 provinces, `finwire_fixed_cost_burden` | `/v1/fixed-cost-burden` | GUS, NBP, KRRiT | monthly | last 36 months with complete inputs |
| [Heating season](https://public-api.finwire.pl/datasets/heating-season): degree days since 1 July vs the 20-season norm, cold index, season cost of a 100 m2 house, Poland and 17 cities, `finwire_heating_season_index`, `finwire_heating_season_cost` | `/v1/heating-season` | IMGW-PIB, Eurostat, URE, GUS | daily | seasons since 1966/67 |
| [Polish public debt and state budget](https://public-api.finwire.pl/datasets/public-debt): State Treasury debt, debt per resident `finwire_public_debt_per_capita`, public debt % of GDP, budget deficit | `/v1/public-debt` | [Ministry of Finance](https://www.gov.pl/web/finanse/szeregiczasowe), GUS, Eurostat | monthly (debt), quarterly (% of GDP), monthly (budget estimate) | debt from January 2001, budget from 2018 |
| [PIT returns by municipality](https://public-api.finwire.pl/datasets/pit-returns): PIT due per resident `finwire_pit_per_capita`, by type of return (PIT-37, PIT-36L, PIT-28, PIT-38), every municipality and province | `/v1/pit/summary`, `/v1/pit/municipalities`, `/v1/pit/municipalities/{teryt}` | [Ministry of Finance](https://dane.gov.pl/pl/dataset/3497), GUS | yearly | tax years from 2022 |
| [Regulated electricity and gas prices](https://public-api.finwire.pl/datasets/energy-tariffs): household tariffs G11, G12, G12w and W-1.1 to W-4 | `/v1/energy/regulated-prices` | [President of URE](https://bip.ure.gov.pl/bip/taryfy-i-inne-decyzje-b) (tariff decisions) | on new decision | tariff years in force |
| [Brokerage account fees](https://public-api.finwire.pl/datasets/broker-fees): commissions on GPW, US and German stocks, account and FX fees | `/v1/brokers` | brokers' official fee tables, compiled by finwire.pl | weekly check | current fee tables |
| [Legal changes radar](https://public-api.finwire.pl/datasets/legal-changes): acts entering into force, bills in the Sejm | `/v1/legal-changes` | [Sejm ELI API](https://api.sejm.gov.pl/eli.html) | daily | upcoming and recent |
| [Residents income by municipality and county](https://public-api.finwire.pl/datasets/municipal-income) | `/v1/income/municipalities` | Ministry of Finance (Rb-27S), [GUS BDL](https://bdl.stat.gov.pl), computed by finwire.pl | yearly, quarterly update | budget years |
| [Housing running costs](https://public-api.finwire.pl/datasets/housing-upkeep) in 20 cities | `/v1/housing/upkeep/{city}` | [GUS BDL](https://bdl.stat.gov.pl) | yearly | latest year |
| [House prices and housing conditions in Europe](https://public-api.finwire.pl/datasets/eu-housing) | `/v1/housing/europe` | [Eurostat](https://ec.europa.eu/eurostat) | quarterly, yearly | since 2010 (conditions) |
| [Bank lending survey](https://public-api.finwire.pl/datasets/credit-survey) | `/v1/credit-market/survey` | [NBP](https://nbp.pl) | quarterly | editions |
| [Household loans and deposits](https://public-api.finwire.pl/datasets/nbp-monetary) | `/v1/credit-market/monetary` | [NBP](https://nbp.pl) | monthly | up to 400 months |
| [Housing loan demand (BIK)](https://public-api.finwire.pl/datasets/bik-credit-demand) | `/v1/credit-market/demand` | [BIK](https://media.bik.pl/informacje-prasowe), [NBP](https://nbp.pl) | monthly | from 2025-08 |
| [Mortgage payment risk and fixed-rate premium](https://public-api.finwire.pl/datasets/rate-risk): chance the variable-rate payment rises by more than 5% in 12 months, payment percentiles in 3, 6 and 12 months, backtest accuracy, `finwire_rate_risk_gauge`, `finwire_fixed_rate_premium` | `/v1/credit-market/rate-risk` | finwire.pl model on [GPW Benchmark](https://gpwbenchmark.pl) WIBOR (result only), bank offers, [ECB MIR](https://data.ecb.europa.eu/data/datasets/MIR) | daily | month ends from 2008 |
| [Interest rates on new loans in Europe](https://public-api.finwire.pl/datasets/ecb-mortgage-rates) | `/v1/credit-market/ecb-rates` | [ECB MIR](https://data.ecb.europa.eu) | monthly | since 2014 |
| [Electricity and gas prices for households in the EU](https://public-api.finwire.pl/datasets/eu-energy-prices): EUR, PPS, PLN, `finwire_eu_power_price_gap`, kWh per minimum wage | `/v1/europe/energy-prices` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/nrg_pc_204/default/table) (nrg_pc_204, nrg_pc_202) | half-yearly | since 2007 |
| [Minimum wages in the EU](https://public-api.finwire.pl/datasets/eu-minimum-wages): EUR, PPS, national currency | `/v1/europe/minimum-wages` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/earn_mw_cur/default/table) (earn_mw_cur) | half-yearly | since 1999 |
| [Economic calendar](https://public-api.finwire.pl/datasets/economic-calendar): MPC (RPP) decisions, CPI, wages, GDP, unemployment | `/v1/calendar` | GUS, NBP, compiled by finwire.pl | continuous | upcoming events |
| [IKE / IKZE contribution limits](https://public-api.finwire.pl/datasets/legal-limits) | `/v1/legal-limits` | Ministry of Family announcements | yearly | current year |
| [Property tax upper limits](https://public-api.finwire.pl/datasets/local-tax) | `/v1/legal-limits/local-tax` | Minister of Finance ([Monitor Polski](https://eli.gov.pl)) | yearly | announced tax years |
| [Deposit](https://public-api.finwire.pl/datasets/deposit-rates), [savings](https://public-api.finwire.pl/datasets/savings-rates), [mortgage](https://public-api.finwire.pl/datasets/mortgage-rates) and [cash loan](https://public-api.finwire.pl/datasets/cash-loan-rates) rate aggregates | `/v1/market/*` | finwire.pl (aggregate of offers on the market) | daily | current |
| [finwire index registry](https://public-api.finwire.pl/datasets/finwire-index): every index computed by finwire.pl under a stable `finwire_*` id ([list](#finwire-indices-finwire_-ids)) | `/v1/finwire-index/{id}` | finwire.pl (method and sources per index) | daily, monthly or quarterly per index | per index |
| [Legal calendar](https://public-api.finwire.pl/datasets/legal-calendar) (`finwire_legal_calendar`): PIT, ZUS and VAT deadlines, IKE/IKZE year end, changes of statutory values, acts entering into force, RPP meetings, retail bond sales | `/v1/legal-calendar` | [Sejm ELI API](https://api.sejm.gov.pl/eli.html), NBP, Ministry of Finance, statutory deadlines, compiled by finwire.pl | daily | upcoming events, ICS at [finwire.pl/kalendarz-zmian.ics](https://finwire.pl/kalendarz-zmian.ics) |
| [KNF public warnings](https://public-api.finwire.pl/datasets/knf-warnings): entries verbatim from KNF, counts by legal basis, entity lookup, weekly new entries (`finwire_knf_warnings_monitor`) | `/v1/knf-warnings` | [KNF, lista ostrzeżeń publicznych](https://www.knf.gov.pl/dla_konsumenta/ostrzezenia_publiczne) | 3 times a day | current list, weekly series since November 2026 |
| [BFG deposit guarantee list](https://public-api.finwire.pl/datasets/bfg-guarantee): banks and credit unions covered by the BFG guarantee, counts by group with history (`finwire_bfg_guarantee_monitor`), latest changes, lookup by name or website | `/v1/bfg-guarantee` | [BFG, lista podmiotów objętych gwarancjami](https://bfg.pl/gwarantowanie-depozytow/podmioty-objete-gwarancjami/) | twice a day | every change of the list since 2019 |
| [Court rulings in credit disputes](https://public-api.finwire.pl/datasets/credit-judgments): rulings of common courts on CHF and other currency mortgages, WIBOR, free credit sanction, early repayment refunds and unit-linked policies, monthly counts, share for the borrower (`finwire_borrower_win_share`), instances, newest rulings with links | `/v1/credit-judgments` | [Court Rulings Portal, Ministry of Justice](https://orzeczenia.ms.gov.pl) via [SAOS](https://www.saos.org.pl) | daily | all published rulings, history filled over the first weeks |
| [Tax rulings](https://public-api.finwire.pl/datasets/tax-rulings): KIS and municipal individual rulings by personal finance topic, weekly counts (`finwire_tax_rulings_pulse`), most cited provisions, newest rulings with links | `/v1/tax-rulings` | [Ministry of Finance, EUREKA](https://eureka.mf.gov.pl) | daily | last 53 weeks, growing |
| [Next-year statutory limits](https://public-api.finwire.pl/datasets/next-year-limits) (`finwire_next_year_limits`): pension indexation, minimum wage, IKE/IKZE limits, ZUS caps and bases, flat-rate tax and small CIT taxpayer limits for the next year from the formulas in law, with stage, 80% interval and backtest | `/v1/legal-limits/next-year` | finwire.pl calculation from GUS and NBP data and acts in the Journal of Laws and Monitor Polski | several times a day | next year, estimate path since June, backtest on past years |
| [Entry threshold](https://public-api.finwire.pl/datasets/entry-threshold): wage needed for a mortgage on a model flat, 17 cities | `/v1/index/entry-threshold` | finwire.pl (prices from notarial deeds in RCN, GUS RCiWN fallback, mortgage offers, GUS wage distribution) | daily | since September 2026 |
| [Finwire Nowcast](https://public-api.finwire.pl/datasets/nowcast): forecasts of CPI and core inflation and the RPP decision | `/v1/macro/nowcast` | finwire.pl model (inputs: GUS, NBP, EC Oil Bulletin) | daily, frozen 3 working days before publication | backtest since 2019 (CPI, core), 2012 (RPP) |
| [finwire lead forecasts](https://public-api.finwire.pl/datasets/lead-forecasts): core inflation in 6-12 months and impaired loans in 2-4 quarters | `/v1/macro/lead-forecasts` | finwire.pl models (inputs: GUS, NBP, Eurostat, KNF) | daily model run, monthly data | rolling backtest, published only above 70% interval coverage |
| finwire indicators: [credit stress map](https://public-api.finwire.pl/datasets/credit-stress), barometers | `/v1/index/*` | finwire.pl (computed from the above) | daily (credit stress), monthly (barometers) | barometers since 2015 |

Aggregates never expose single offers, deeds or listings - only medians, percentiles and counts.

---

## Use cases

- **Mortgage and savings calculators** - NBP reference rate and market median mortgage rates in one call (`/v1/snapshot`).
- **Newsroom and research charts** - CPI since 1982, unemployment since 2011, bond yields since 2001, with a ready citation in `meta`.
- **Real estate analysis** - transaction prices per district, rents, rental yield and housing supply for 17 cities.
- **Labour market and HR** - wages by occupation with deciles and the gender pay gap, unemployment by county.
- **Personal finance apps** - IKE/IKZE limits, retail bond rates, deposit and savings rate medians.
- **AI assistants and agents** - an [MCP server](#mcp-server-for-ai-assistants) (Streamable HTTP, no key) serves rates, FX, housing prices, rents, the credit market, legal limits, law changes and macro data to Claude, ChatGPT, Cursor, VS Code and other MCP clients; [llms.txt](https://public-api.finwire.pl/llms.txt) and the OpenAPI spec describe every dataset in machine-readable form.

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

## MCP server for AI assistants

`https://public-api.finwire.pl/mcp` - Model Context Protocol over Streamable HTTP, stateless, no key, read-only. The same data, whitelist and licenses as `/v1`. What it answers, with a coverage table and example questions: [finwire.pl/en/mcp](https://finwire.pl/en/mcp) (Polish: [finwire.pl/mcp](https://finwire.pl/mcp)).

| Client | How to connect |
|---|---|
| Claude (claude.ai, Desktop) | Settings - Connectors - Add custom connector - URL `https://public-api.finwire.pl/mcp` |
| Claude Code | `claude mcp add --transport http finwire https://public-api.finwire.pl/mcp` |
| ChatGPT | Connectors in developer mode - create a connector with the URL above, no authentication |
| Cursor | `~/.cursor/mcp.json`: `{"mcpServers": {"finwire": {"url": "https://public-api.finwire.pl/mcp"}}}` |
| VS Code | `.vscode/mcp.json`: `{"servers": {"finwire": {"type": "http", "url": "https://public-api.finwire.pl/mcp"}}}` |

Tools (all read-only, each with `outputSchema`):

- Macro: `get_macro_snapshot`, `list_macro_indicators`, `get_macro_indicator`, `compare_macro_indicators`, `get_macro_changes`, `get_macro_calendar`
- Rates and prices: `get_interest_rates` (NBP), `get_currency_rates` (NBP FX, gold), `get_fuel_prices`, `get_energy_prices` (URE electricity and gas tariffs), `get_retail_prices` (GUS shop prices, finwire shopping basket)
- Subsidies: `get_subsidy_stats` (Czyste Powietrze heating subsidy programme: applications, contracts, payouts, payout queue, `finwire_clean_air_payout_rate`)
- Pensions: `get_pension_stats` (average ZUS pension gross and net, other pensions, allowances, `finwire_pension_wage_ratio`)
- Typical person: `get_average_pole` (Average Pole `finwire_average_pole`: median age, wage, flat, mortgage and model payment, pension, spending, car age, top brand; each trait with measure and period)
- Online prices: `get_daily_price_index` (experimental Daily price index `finwire_daily_price_index`: online shelf prices of a fixed basket, base month = 100, categories, largest rise and fall, food part vs GUS CPI food; no single product prices)
- Pension forecast: `get_pension_forecast` (model of the future ZUS pension `finwire_pension_forecast`: replacement rate and pension in today's money by birth year 1966-2006, profile k60/k65/m65 and wage level, P10/P50/P90, effect of the shrinking workforce and longer life, OECD and EC benchmarks)
- Businesses: `get_business_registrations` (applications in CEIDG to register, close, suspend and resume a sole proprietorship, `finwire_new_business_ratio`)
- Cars: `get_vehicle_registrations` (first registrations by type, province and passenger-car brand, share of brands from China, `finwire_car_registrations_pulse`, `finwire_chinese_brands_share`), `get_electric_vehicles` (BEV and PHEV fleet and new cars, counties, charging vs EV correlation, `finwire_ev_share_new_cars`), `get_ev_charging` (EIPA charging points, fast sites, `finwire_ev_charging_coverage`, AFIR power classes with median prices), `find_ev_chargers` (chargers near a city or point: power, free now, price per kWh, one site card), growth of charging infrastructure in `get_ev_charging` (`finwire_ev_infrastructure_growth`), `get_ev_charging_prices` (price per kWh by power class and network, price changes, `finwire_ev_charging_price_index`)
- PPK: `get_ppk_stats` (Employee Capital Plans from the PFR Portal PPK bulletin: net assets, active accounts, participants, participation rate, average fee, `finwire_ppk_avg_account`)
- Retail bonds vs deposits: `get_retail_bond_premium` (first-year premium of each retail savings bond series over the best deposits, savings accounts and the average new deposit after tax, retail bond sales, `finwire_retail_bond_premium`)
- Household budget: `get_fixed_cost_burden` (share of net income taken by mortgage, admin fee, utilities and TV licence, Poland and 16 provinces, `finwire_fixed_cost_burden`)
- Heating season: `get_heating_season` (degree days vs norm, cold index, season forecast and heating cost of a 100 m2 house, Poland and 17 cities, `finwire_heating_season_index`, `finwire_heating_season_cost`)
- Investment funds: `get_investment_funds` (IZFiA assets, inflows, outflows and net flows by fund group, PPK, `finwire_fund_flows_index`)
- IKE and IKZE: `get_ike_ikze_stats` (KNF half-yearly accounts, assets and contributions by institution type, limit usage, `finwire_ike_ikze_avg_balance`)
- Public debt: `get_public_debt` (State Treasury debt, debt per resident `finwire_public_debt_per_capita`, public debt % of GDP, state budget deficit)
- finwire indices: `list_finwire_indices` (all `finwire_*` ids with the latest reading), `get_finwire_index` (one index by id or alias, with history and citation)
- Forecasts: `get_nowcast` (Finwire Nowcast: CPI and core inflation, RPP decision probabilities, accuracy)
- Lead forecasts: `get_lead_forecasts` (core inflation in 6-12 months `finwire_cost_pressure`, impaired housing and consumer loans in 2-4 quarters `finwire_npl_forecast`; shown only above the accuracy threshold)
- Housing: `get_housing_prices` (notarial deeds, cities and districts), `get_rents` (rents, rental yield), `get_housing_costs`, `get_housing_balance` (`finwire_housing_pressure` new residents per new dwelling, `finwire_housing_pipeline` completions forecast from building permits, `finwire_rent_forecast` 6-month rent forecast)
- Credit, savings and investing: `get_credit_market`, `get_rate_risk` (mortgage payment risk in 3, 6 and 12 months with backtest, `finwire_rate_risk_gauge`; fixed-rate premium `finwire_fixed_rate_premium`), `get_bank_rates` (aggregates only, no single offers; retail bonds), `get_broker_fees` (brokerage fee tables)
- Law: `get_legal_limits` (IKE/IKZE, PIT, minimum wage, local tax limits), `get_next_year_limits` (next-year statutory values from legal formulas before the act, `finwire_next_year_limits`), `get_legal_changes` (acts, bills in the Sejm and the next 30 days of `finwire_legal_calendar`), `get_tax_rulings` (tax rulings by personal finance topic), `check_knf_warnings` (a firm or website on the KNF public warnings list), `check_bfg_guarantee` (a bank or credit union on the BFG deposit guarantee list)
- Business in Poland (also for founders from abroad): `plan_business_entry` (forms available to the owner with reasons, taxes and net per form with the treaty dividend rate, setup steps, residence, deadlines), `get_business_forms`, `calc_net_income` (employment, mandate, work contract, B2B), `compare_business_forms` (JDG vs sp. z o.o.), `calc_employer_cost`, `calc_zus_contributions`, `get_tax_treaty` (withholding tax, treaty rates), `get_compliance_calendar`, `get_business_guide` (verified facts with legal basis), `get_business_glossary`
- Regions and Europe: `get_regional_data` (income and PIT per resident by municipality, unemployment, wages), `get_europe_data` (house prices, mortgage rates, bond yields, household electricity and gas prices, minimum wages in the EU)
- Any dataset: `search_datasets`, `get_dataset`

Every result carries the period of the data, the original source, `license`, a ready `citation` and `url` - the finwire.pl page with the full table, chart and calculator (with `utm_source=mcp`). Resources: `macro://catalog`, `finwire://datasets`, `finwire://dataset/{id}`; prompts: `macro_briefing`, `explain_indicator`, `city_housing_report`, `personal_finance_limits`.

Discovery: [server card](https://public-api.finwire.pl/mcp/server-card) (`application/mcp-server-card+json`), [/.well-known/ard.json](https://public-api.finwire.pl/.well-known/ard.json), registry manifest [server.json](server.json).

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
| `GET /v1/housing/location-premium/{city}` | Districts of a city: actual vs expected price per m² from neighbourhood quality, premium %, model fit (`finwire_location_premium`) |
| `GET /v1/local-tax-squeeze` | Property tax as % of property tax + PIT share in municipal budgets: Poland, provinces, cities, municipalities in the squeeze (`finwire_local_tax_squeeze`); `/municipalities` for every municipality |
| `GET /v1/jst-budget/municipalities` | Municipal budgets: spending, debt and EU funding per resident of every municipality (`finwire_gmina_spending_per_capita`); `/municipalities/{teryt}` for one municipality by category vs neighbours, similar municipalities and Poland; `/ranking` by metric |
| `GET /v1/series/housing` and `/{city}` | GUS housing prices per m² for 17 cities, latest quarters, nominal and real YoY |
| `GET /v1/rent` and `/{city}` | Asking rents: median, mean, per m², month-over-month change, districts |
| `GET /v1/rent/yield` | Gross and net rental yield and payback period per city |
| `GET /v1/housing/carry` | Housing carry per city: net rental yield minus mortgage rate, monthly (`finwire_housing_carry`) |
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
| `GET /v1/legal-limits/next-year?path=false` | Next-year statutory values from the formulas in law (`finwire_next_year_limits`): value, 80% interval, stage (projekcja, projekt, obwieszczenie, obowiazuje), calculation, inputs, estimate path and backtest |
| `GET /v1/macro/indicators` | Headline indicators with the latest reading, change, sparkline and source |
| `GET /v1/macro/indicators/{key}/history?from=2020-01` | Full history of one indicator |
| `GET /v1/macro/consumer-sentiment` | GUS BWUK, WWUK and components with the history |
| `GET /v1/wages/enterprise` | GUS average wage in the enterprise sector, real change, by province |
| `GET /v1/series/gold` | NBP gold price: latest, period changes, all-time high, 12-month average |
| `GET /v1/series/gold/history?range=1y` | Gold price history, `range`: `1m`, `1y`, `5y`, `max` |
| `GET /v1/fuel-prices` | Maximum fuel prices: in force today, announced for tomorrow, history |
| `GET /v1/prices/retail` | Average retail prices of 30 goods and services (GUS), changes, minutes of work, finwire shopping basket; `?product=` for one price history |
| `GET /v1/subsidies/clean-air` | Czyste Powietrze programme (NFOŚiGW): applications, contracts and payouts cumulative, Modernisation Fund edition, last week, payout queue and pace, heat sources, reading history |
| `GET /v1/pensions` | Average ZUS pension gross and net, other pensions, allowances, pension to wage ratio, monthly history |
| `GET /v1/average-pole` | Average Pole (`finwire_average_pole`): traits of the typical person in Poland with measure (median, mean, model, distribution, mode), period and source |
| `GET /v1/daily-price-index` | Daily price index finwire (`finwire_daily_price_index`, experimental): daily series, monthly readings with m/m, basket categories, food part vs GUS CPI food, collection state and basket list without prices |
| `GET /v1/pension-forecast` | Pension forecast finwire (`finwire_pension_forecast`): headline (man, average wage, retiring 30 years after the base year), grid birth year x profile x wage level with replacement rates and pension in today's money (P10/P50/P90), fan by retirement year, decomposition, working-age population, OECD and EC benchmarks, parameters |
| `GET /v1/business-registrations?months=24` | Applications filed in CEIDG (MRiT): registrations, closures, suspensions and resumptions of sole proprietorships, net, same month in earlier years, share of women, `finwire_new_business_ratio`, monthly history |
| `GET /v1/vehicle-registrations?months=24&province=14` | First registrations in Poland (Ministry of Digital Affairs, CEPiK data): passenger cars and all vehicles, 17 types, provinces per 1000 residents, held-back months, brands of the month and 12 months, share of brands from China, `finwire_car_registrations_pulse`, `finwire_chinese_brands_share` |
| `GET /v1/electric-vehicles?counties=all` | BEV and PHEV passenger cars: fleet and new cars by year (Eurostat), fuel mix of new cars, provinces and counties per 1000 cars (GUS BDL), top and bottom counties, correlation of charging sites and EVs with a version controlling wages, `finwire_ev_share_new_cars` |
| `GET /v1/ev-charging/chargers?city=Łódź&minKw=150` | EV charging sites (UDT EIPA, end-user data account) by city or point and radius: power class, kW, points free now, lowest ad hoc price per kWh; `/v1/ev-charging/chargers/{id}` = one site with points, connectors, prices and status |
| `GET /v1/ev-charging/prices` | Ad hoc charging prices per kWh by AFIR power class, AC vs DC, networks, cities, recent changes, `finwire_ev_charging_price_index` |
| `GET /v1/ev-charging/networks` | Charging networks: stations, points, median prices, reliability, ranking with weights; `/v1/ev-charging/networks/{slug}` = one network price list, `finwire_ev_charger_reliability` |
| `GET /v1/ev-charging/occupancy` | Charger occupancy now, weekly trend and by weekday x hour, busiest sites, cities and networks, `finwire_ev_charger_occupancy` (full weeks, after 14 days of readings) |
| `GET /v1/ev-charging/growth?network=greenway` | Growth of charging infrastructure: EIPA month ends since December 2019 with monthly change, year on year and records, CNG/LNG; daily sites and points by power class, province and network (or one network / city), `finwire_ev_infrastructure_growth` |
| `GET /v1/ev-charging/coverage?counties=all` | Distance from each county to the nearest charging site from 150 kW, `finwire_ev_fast_coverage` |
| `GET /v1/ev-charging/gas` | CNG, LNG and hydrogen stations with prices per m3 or kg |
| `GET /v1/ev-charging?months=24` | Public EV charging (UDT EIPA): points, stations and locations today and at month ends since December 2019, sites and fast sites by province and county, `finwire_ev_charging_coverage` |
| `GET /v1/ppk?months=12` | PPK (Employee Capital Plans) from the monthly PFR Portal PPK bulletin: net assets, active accounts, participants, employers, participation rate, average fee, `finwire_ppk_avg_account`, monthly history |
| `GET /v1/retail-bond-premium?months=12` | Premia obligacji skarbowych finwire: retail savings bonds vs the finwire deposit and savings indices and the NBP average new deposit, after tax, per series, retail bond debt change, sales response test |
| `GET /v1/fixed-cost-burden?region=mazowieckie` | Sztywne koszty gospodarstwa finwire: share of household net income taken by fixed costs, Poland and 16 provinces, components in PLN, monthly history |
| `GET /v1/ike-ikze` | IKE and IKZE (KNF, half-yearly): accounts, assets, contributions, withdrawals and returns by institution type, holders by sex and age, average contribution vs annual limit, `finwire_ike_ikze_avg_balance`, history in PLN |
| `GET /v1/investment-funds?months=24` | Investment funds (IZFiA): assets, purchases, redemptions and net flows of the market and fund groups, bond types, equity regions, `finwire_fund_flows_index`, monthly history in PLN million |
| `GET /v1/public-debt` | State Treasury debt monthly since 2001, debt per resident, growth per second, structure, public debt % of GDP, budget deficit with plan |
| `GET /v1/energy/regulated-prices?date=2026-10-29` | Household electricity and gas tariffs approved by URE in force on a day (default today): energy and distribution rates, fees, cheaper-zone hours, decision and PDF per row; net amounts |
| `GET /v1/brokers` | Broker fee tables: commissions by market, account and FX fees, IKE/IKZE, active promotions, fee table URL and weekly check status |
| `GET /v1/legal-changes?days=90` | Acts entering into force soon and recently, open bills in the Sejm |
| `GET /v1/legal-changes/process/{term}/{number}` | One bill: last stage, passage, published act |
| `GET /v1/legal-calendar?kategorie=przepisy,wartosci&od=2026-11-01&do=2027-03-31` | Legal calendar `finwire_legal_calendar`: dated events with category, legal basis, source and finwire.pl page, the number of changes in acts and values in the next 30 days. `kategorie`: `przepisy`, `wartosci`, `podatki`, `firma`, `oszczednosci`, `obligacje`, `stopy` |
| `GET /v1/knf-warnings` | KNF public warnings list: entries by legal basis, prosecutors, new entries per full week (`finwire_knf_warnings_monitor`), newest entries verbatim from KNF |
| `GET /v1/bfg-guarantee` | BFG deposit guarantee list: counts of banks and credit unions with history (`finwire_bfg_guarantee_monitor`), latest changes, EUR limit and its PLN equivalent; `/search?q=` looks up an entity |
| `GET /v1/credit-judgments?category=wibor` | Court rulings in WIBOR disputes: monthly counts, share ruled for the borrower at least in part (`finwire_borrower_win_share`), instance I vs II, newest rulings with case number, court, outcome and Court Rulings Portal link |
| `GET /v1/tax-rulings?topic=najem_prywatny` | Tax rulings on a personal finance topic: weekly counts (`finwire_tax_rulings_pulse`), most cited provisions, newest rulings with signature, authority, shortened thesis and EUREKA link |
| `GET /v1/knf-warnings/search?q=tradereu.com` | Look up a firm by name, website, KRS, NIP or REGON; `onList: false` does not mean the firm holds a KNF licence |
| `GET /v1/income/municipalities?year=2025` | Residents income per capita by municipality: rank, ratio to the national average, change |
| `GET /v1/income/counties?year=2025` | The same by county |
| `GET /v1/housing/upkeep/{city}` | Monthly running costs of a flat in a city (list: `/v1/housing/upkeep`) |
| `GET /v1/housing/europe` | Eurostat house price index: Poland vs EU vs euro area and country ranking |
| `GET /v1/housing/europe/overburden` and `/overcrowding` | Housing cost overburden and overcrowding rates by country |
| `GET /v1/europe/energy-prices?product=electricity&compare=DE` | Household electricity or gas prices per kWh in EU countries (Eurostat, half-yearly): ranking in EUR and PPS, Poland in PLN, EU average, history since 2007, `finwire_eu_power_price_gap`, kWh per monthly minimum wage |
| `GET /v1/europe/minimum-wages?compare=DE` | Monthly statutory minimum wages in EU countries (Eurostat, half-yearly): ranking in EUR and PPS, medians, history of Poland and a comparison country |
| `GET /v1/credit-market/survey?quarters=12` | NBP bank lending survey |
| `GET /v1/credit-market/monetary?months=25` | NBP household loans, deposits and new loans |
| `GET /v1/credit-market/demand?months=12` | BIK housing loan demand, requested vs granted amount, granted loans (BIK) and new housing loans (NBP) |
| `GET /v1/credit-market/rate-risk?days=30` | Ryzyko raty finwire (chance the payment rises by more than 5% or 10% in 3, 6 and 12 months, payment percentiles, backtest since 2008) and Premia za stałą stopę finwire (5-year fixed minus expected variable cost, daily and monthly from ECB MIR, negative premium windows) |
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
| `GET /v1/finwire-index` | All finwire indices: id `finwire_*`, name, group, status, latest and previous reading with periods, change and its kind (`pp`, `pct`, `abs`), method version, sources, `stale`, `pageUrl`, `methodologyUrl`, `citation` |
| `GET /v1/finwire-index/{id}?limit=120&from=2024-01` | One index by `finwire_*` id or alias, with history (`limit` 1-600, default 120; `from` YYYY-MM); `requestedId` echoes the id used |
| `GET /v1/macro/nowcast` | Finwire Nowcast: next CPI and core inflation reading (point, 80% interval) and RPP decision probabilities, freeze date and status, settled history vs actual readings, backtest and live accuracy |
| `GET /v1/macro/lead-forecasts?index=npl` | finwire lead forecasts: NBP core inflation in 6 and 12 months (`finwire_cost_pressure`) and share of impaired (Stage 3) housing and consumer loans in 2 and 4 quarters (`finwire_npl_forecast`), 80% intervals, rolling backtest; numbers withheld (`published: false`) below 70% interval coverage |
| `GET /v1/index/entry-threshold` | Gross monthly wage per person needed for a mortgage on a model flat (parameters in `params`) within KNF DSTI limits in 17 cities, the installment, and the share of employees earning at least that |
| `GET /v1/index/entry-threshold/history?household=single` | Daily change points per city, `household`: `single` or `couple` |
| `GET /v1/index/credit-stress` | Mortgage installment for a 50 m² flat as % of the average net wage in 16 provinces, with KNF Recommendation S DSTI thresholds |
| `GET /v1/index` | All barometers with value, position against history and monthly change |
| `GET /v1/index/{slug}` | One barometer with monthly series, methodology and best/worst month since 2015. `slug`: `real-deposit-rate`, `housing-affordability`, `credit-cost`, `borrowing-power`, `purchasing-power` |
| `GET /v1/snapshot` | Rates, inflation, FX, credit stress and barometers in one call |

Full field-level schemas: [openapi.json](https://public-api.finwire.pl/openapi.json).

### finwire indices (`finwire_*` ids)

Every index computed by finwire.pl has one stable id, the same in this API, in MCP, in CSV files on finwire.pl (column `series_id`) and in the page JSON-LD (`identifier`). Old dataset ids are accepted as aliases by `/v1/finwire-index/{id}`. The live list with readings: [`/v1/finwire-index`](https://public-api.finwire.pl/v1/finwire-index); hub with charts: [finwire.pl/indeksy](https://finwire.pl/indeksy).

| id | Index | Group | Reading | Aliases |
|---|---|---|---|---|
| `finwire_mortgage_barometer` | [Barometr kredytu hipotecznego finwire](https://finwire.pl/barometr-kredytu-hipotecznego) | credit | daily | - |
| `finwire_credit_capacity_index` | [Indeks zdolności kredytowej finwire](https://finwire.pl/indeksy/zdolnosc-kredytowa) | credit | monthly | - |
| `finwire_borrowing_power_barometer` ¹ | [Barometr zdolności kredytowej finwire](https://finwire.pl/barometr-zdolnosci-kredytowej) | credit | monthly | `borrowing-power` |
| `finwire_mortgage_cost_barometer` ¹ | [Barometr kosztu kredytu finwire](https://finwire.pl/barometr-kosztu-kredytu-hipotecznego) | credit | monthly | `credit-cost` |
| `finwire_rate_risk_gauge` ¹ | [Ryzyko raty finwire](https://finwire.pl/indeksy/ryzyko-raty) | credit | daily | `rate-risk` |
| `finwire_fixed_rate_premium` ¹ | [Premia za stałą stopę finwire](https://finwire.pl/indeksy/ryzyko-raty) | credit | daily | `rate-risk` |
| `finwire_credit_market_index` ¹ | [Wskaźnik rynku kredytów hipotecznych finwire](https://finwire.pl/wskaznik-rynku-kredytow) | credit | monthly | `credit-market-index`, `credit-market` |
| `finwire_npl_forecast` ¹ | [Stres kredytowy wyprzedzający finwire](https://finwire.pl/mapa-stresu-kredytowego#kredyty-zagrozone) | credit | monthly (forecast) | - |
| `finwire_credit_stress_index` ¹ | [Mapa stresu kredytowego finwire](https://finwire.pl/mapa-stresu-kredytowego) | credit | daily | `credit-stress` |
| `finwire_deposit_rate_index` | [Indeks lokat finwire](https://finwire.pl/indeksy/lokaty) | savings | monthly | `deposit-rate-index`, `deposit_rate_index` |
| `finwire_savings_rate_index` | [Indeks kont oszczędnościowych finwire](https://finwire.pl/indeksy/konta-oszczednosciowe) | savings | monthly | - |
| `finwire_real_deposit_rate_barometer` | [Barometr opłacalności lokat finwire](https://finwire.pl/barometr-oplacalnosci-lokat) | savings | monthly | `real-deposit-rate` |
| `finwire_purchasing_power_barometer` | [Barometr siły nabywczej złotówki finwire](https://finwire.pl/barometr-sily-nabywczej-zlotowki) | savings | monthly | `purchasing-power` |
| `finwire_account_cost_index` | [Koszt konta osobistego wg finwire](https://finwire.pl/rankingi/konta-osobiste) | savings | monthly | - |
| `finwire_household_inflation_index` | [Inflacja gospodarstw domowych finwire](https://finwire.pl/indeksy/inflacja-gospodarstw-domowych) | savings | monthly | - |
| `finwire_pension_wage_ratio` | [Emerytura do płacy finwire](https://finwire.pl/przecietna-emerytura) | savings | monthly | - |
| `finwire_fund_flows_index` | [Indeks napływów do funduszy finwire](https://finwire.pl/fundusze-inwestycyjne) | savings | monthly | - |
| `finwire_new_business_ratio` | [Wskaźnik nowych firm finwire](https://finwire.pl/finanse-firmy) | market | monthly | - |
| `finwire_car_registrations_pulse` | [Puls rejestracji aut finwire](https://finwire.pl/rejestracje-samochodow) | market | monthly | - |
| `finwire_chinese_brands_share` | [Udział marek z Chin finwire](https://finwire.pl/rejestracje-samochodow) | market | monthly | - |
| `finwire_ev_share_new_cars` | [Udział elektryków w nowych autach finwire](https://finwire.pl/samochody-elektryczne) | market | yearly | - |
| `finwire_ev_charging_coverage` | [Nasycenie ładowarkami finwire](https://finwire.pl/samochody-elektryczne) | market | monthly | - |
| `finwire_ev_charging_price_index` | [Indeks cen ładowania finwire](https://finwire.pl/indeksy) | market | monthly | - |
| `finwire_ev_charger_occupancy` | [Obłożenie ładowarek finwire](https://finwire.pl/oblozenie-ladowarek) | market | weekly (full week) | - |
| `finwire_ev_charger_reliability` | [Sprawność ładowarek finwire](https://finwire.pl/indeksy) | market | daily | - |
| `finwire_ev_fast_coverage` | [Zasięg ultraszybkich ładowarek finwire](https://finwire.pl/indeksy) | market | daily | - |
| `finwire_ev_infrastructure_growth` | [Przyrost ładowarek finwire](https://finwire.pl/mapa-ladowarek#przyrost) | market | monthly | - |
| `finwire_ppk_avg_account` | [Średnie konto PPK finwire](https://finwire.pl/kalkulatory/ppk) | savings | monthly | - |
| `finwire_retail_bond_premium` | [Premia obligacji skarbowych finwire](https://finwire.pl/indeksy/premia-obligacji-skarbowych) | savings | monthly | - |
| `finwire_fixed_cost_burden` | [Sztywne koszty gospodarstwa finwire](https://finwire.pl/indeksy/sztywne-koszty-gospodarstwa) | housing | monthly | - |
| `finwire_heating_season_index` | [Indeks chłodu sezonu finwire](https://finwire.pl/sezon-grzewczy) | housing | daily | - |
| `finwire_heating_season_cost` | [Koszt sezonu grzewczego finwire](https://finwire.pl/sezon-grzewczy#koszt-sezonu) | housing | daily | - |
| `finwire_ike_ikze_avg_balance` | [Średnie saldo IKE i IKZE finwire](https://finwire.pl/kalkulatory/ike-ikze) | savings | half-yearly | - |
| `finwire_clean_air_payout_rate` | [Wskaźnik wypłat Czystego Powietrza finwire](https://finwire.pl/czyste-powietrze) | housing | weekly | - |
| `finwire_public_debt_per_capita` | [Dług na osobę finwire](https://finwire.pl/dlug-publiczny) | market | monthly | - |
| `finwire_pit_per_capita` | [PIT na mieszkańca finwire](https://finwire.pl/najbogatsze-gminy#podatek-pit) | market | yearly | - |
| `finwire_rcn_price_index` | [Indeks cen transakcyjnych finwire](https://finwire.pl/raporty/indeks-cen-transakcyjnych) | housing | quarterly | - |
| `finwire_price_gap_index` | [Indeks luki cenowej finwire](https://finwire.pl/indeksy/luka-cenowa) | housing | quarterly | - |
| `finwire_entry_threshold_index` ¹ | [Próg wejścia finwire](https://finwire.pl/prog-wejscia) | housing | daily | `entry-threshold` |
| `finwire_housing_affordability_barometer` ¹ | [Barometr dostępności mieszkania finwire](https://finwire.pl/barometr-dostepnosci-mieszkania) | housing | monthly | `housing-affordability` |
| `finwire_housing_carry` ¹ | [Carry nieruchomości finwire](https://finwire.pl/indeksy/carry-nieruchomosci) | housing | monthly | `housing-carry` |
| `finwire_housing_permits_index` | [Indeks pozwoleń na budowę finwire](https://finwire.pl/pozwolenia-na-budowe) | housing | monthly | - |
| `finwire_housing_pressure` | [Presja demograficzna na mieszkania finwire](https://finwire.pl/indeksy/presja-demograficzna) | housing | yearly | - |
| `finwire_housing_pipeline` | [Podaż mieszkań za 2 lata finwire](https://finwire.pl/indeksy/podaz-mieszkan) | housing | quarterly | - |
| `finwire_rent_forecast` | [Prognoza czynszów finwire](https://finwire.pl/indeksy/podaz-mieszkan#czynsze) | housing | monthly | - |
| `finwire_offer_ticker` | [Ticker zmian ofert finwire](https://finwire.pl/zmiany-ofert) | market | daily | `offer-changes` |
| `finwire_demand_index` | [Indeks popytu finwire](https://finwire.pl/indeksy/popyt) | market | monthly | - |
| `finwire_power_price_index` | [Indeks ceny prądu finwire](https://finwire.pl/ceny-pradu-jutro) | market | daily | - |
| `finwire_shopping_basket_index` | [Koszyk zakupowy finwire](https://finwire.pl/ceny-w-sklepach) | market | monthly | - |
| `finwire_eu_power_price_gap` | [Luka cen prądu Polska-UE finwire](https://finwire.pl/ceny-pradu-w-europie) | market | half-yearly | - |
| `finwire_nowcast` | [Finwire Nowcast](https://finwire.pl/nowcast) | market | monthly | `nowcast` |
| `finwire_cost_pressure` | [Presja kosztowa finwire](https://finwire.pl/nowcast#presja-kosztowa) | market | monthly (forecast) | - |
| `finwire_legal_calendar` | [Kalendarz zmian w finansach finwire](https://finwire.pl/kalendarz-zmian) | law | daily | - |
| `finwire_knf_warnings_monitor` | [Monitor ostrzeżeń KNF finwire](https://finwire.pl/ostrzezenia-knf) | law | weekly | `knf-warnings` |
| `finwire_bfg_guarantee_monitor` | [Licznik instytucji z gwarancją BFG finwire](https://finwire.pl/rankingi/lokaty#gwarancja-bfg) | savings | on every list change | `bfg-guarantee` |
| `finwire_tax_rulings_pulse` | [Puls interpretacji podatkowych finwire](https://finwire.pl/rozliczenie-pit) | law | weekly | `tax-rulings` |
| `finwire_borrower_win_share` | [Wyroki dla kredytobiorców finwire](https://finwire.pl/wyroki-kredytowe) | law | monthly | `credit-judgments` |
| `finwire_next_year_limits` | [Limity na przyszły rok finwire](https://finwire.pl/indeksy/limity-na-przyszly-rok) | law | daily | `next-year-limits` |
| `finwire_local_tax_squeeze` | [Nożyce fiskalne gmin finwire](https://finwire.pl/najbogatsze-gminy#nozyce-fiskalne) | housing | yearly | `local-tax-squeeze` |
| `finwire_gmina_spending_per_capita` | [Wydatki gmin na mieszkańca finwire](https://finwire.pl/budzet-gminy) | market | yearly | `jst-budget` |
| `finwire_average_pole` | [Przeciętny Polak finwire](https://finwire.pl/przecietny-polak) | credit | monthly | `average-pole` |
| `finwire_daily_price_index` | [Dzienny indeks cen finwire](https://finwire.pl/dzienny-indeks-cen) | market | daily | `daily-price-index` |
| `finwire_pension_forecast` | [Prognoza emerytur finwire](https://finwire.pl/prognoza-emerytury) | savings | yearly | `pension-forecast` |
| `finwire_location_premium` | [Premia za okolicę finwire](https://finwire.pl/ceny-mieszkan/warszawa#premia-za-okolice) | housing | quarterly | `location-premium` |

¹ Computed from WIBOR (GPW Benchmark): only the result is published, never WIBOR or POLSTR (`usesBenchmarkRate: true`).

License CC BY 4.0, attribution finwire.pl. Each index carries a ready `citation` with the id and method version, e.g.:

> Source: Indeks zdolności kredytowej finwire (finwire_credit_capacity_index, method v1), finwire.pl, https://finwire.pl/indeksy/zdolnosc-kredytowa, CC BY 4.0

`methodVersion` changes with every change of the definition; history is recomputed or marked.

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
least that. It is recomputed every night from prices per m² in notarial deeds (Real Estate
Price Register, GUS RCiWN as a fallback for small samples), current mortgage offers and the GUS
wage distribution; `/history` shows how it changed. Index id: `finwire_entry_threshold_index`.

### How do I cite a finwire index?

Use the `citation` field of `/v1/finwire-index/{id}`: index name, `finwire_*` id, method
version, finwire.pl and the page URL, CC BY 4.0. The id stays the same when the name or the
method changes, so it is the safe key for your own database.

### Is there an API for Polish tax and ZUS deadlines?

`/v1/legal-calendar` returns PIT, ZUS and VAT deadlines (moved to the next working day per
art. 12 § 5 of the Tax Ordinance), the year end for IKE and IKZE contributions, changes of
statutory values, acts entering into force, RPP meetings and retail bond sales. The same
calendar is available as an ICS subscription at https://finwire.pl/kalendarz-zmian.ics.

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
- **finwire_\*** - stable id of an index computed by finwire.pl, the key in `/v1/finwire-index`, MCP and CSV `series_id`.
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

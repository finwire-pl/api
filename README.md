| `GET /v1/pension-earning-limits?quarters=8` | Pension earning limits: 70% and 130% of the average wage (ZUS announcements, finwire estimate from GUS before them), maximum reductions, annual thresholds, history |
| [Pension earning limits](https://public-api.finwire.pl/datasets/pension-earning-limits): how much a pensioner can earn - 70% and 130% of the average wage for the quarter, ZUS announcement or finwire estimate from GUS before it, maximum reductions, annual thresholds | `/v1/pension-earning-limits` | [Monitor Polski](https://api.sejm.gov.pl/eli/acts/search?publisher=MP&title=kwot%20przychodu) (ZUS and GUS announcements) | quarterly | from Q4 2015 |
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
| [Most expensive currencies against PLN](https://public-api.finwire.pl/datasets/fx-ranking) (NBP tables A and B of the same day, place 52 weeks earlier) | `/v1/series/fx-ranking` | [NBP](https://nbp.pl/statystyka-i-sprawozdawczosc/kursy/tabela-b/) | weekly (table B) | current and 52 weeks earlier |
| [10-year government bond yields](https://public-api.finwire.pl/datasets/bond-yields): PL, DE, euro area, EU + spreads | `/v1/series/bond-yields/{geo}` | [Eurostat irt_lt_mcby_m](https://ec.europa.eu/eurostat/databrowser/view/irt_lt_mcby_m/default/table) | monthly | since 2001 |
| [Retail government bonds](https://public-api.finwire.pl/datasets/bonds) (OTS, ROR, DOR, TOS, COI, EDO, ROS, ROD) | `/v1/bonds` | [Ministry of Finance](https://www.obligacjeskarbowe.pl) | monthly issues | current issues |
| [Flat transaction prices per m²](https://public-api.finwire.pl/datasets/transaction-prices) (median, quartiles, primary/secondary) - 17 cities and their districts | `/v1/housing/transactions/{city}` | Real Estate Price Register (RCN, [GUGiK](https://www.geoportal.gov.pl) + city registers) | quarterly, ingested daily | last 12 quarters per city |
| [Flat prices in municipalities](https://public-api.finwire.pl/datasets/municipality-flat-prices) (deeds, median and quartiles per m² from 30 deeds, primary/secondary, deeds per 1000 residents) - every gmina with deeds | `/v1/housing/municipalities/{teryt}` | Real Estate Price Register (RCN, [GUGiK](https://www.geoportal.gov.pl) + city registers), population GUS | quarterly, ingested daily | quarters since 2010 |
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
| [Gold price by fineness](https://public-api.finwire.pl/datasets/gold-fineness): 1 g of gold 999, 960, 750, 585, 500, 375, 333 and troy ounce in USD | `/v1/series/gold/fineness` | [NBP](https://nbp.pl/statystyka-i-sprawozdawczosc/kursy/cena-zlota/) (gold price, USD mid rate table A) | daily (business days) | since 2013 |
| [Maximum fuel prices](https://public-api.finwire.pl/datasets/fuel-max-prices): Pb95, Pb98, diesel | `/v1/fuel-prices` | Minister of Energy ([Monitor Polski](https://eli.gov.pl)) | on announcement | all announcements |
| [Crude oil Brent and WTI](https://public-api.finwire.pl/datasets/oil-prices): spot prices in USD and PLN per barrel, `finwire_brent_pln`, crude cost per litre next to Polish pump prices | `/v1/oil-prices` | [U.S. Energy Information Administration](https://www.eia.gov/petroleum/prices/), NBP, European Commission | weekly (Wednesday) | full daily history (PLN from the first NBP rate in the dataset) |
| [Pump price estimate for tomorrow](https://public-api.finwire.pl/datasets/fuel-retail-estimate): average petrol 95 and diesel price at Polish stations from wholesale prices, station margin and VAT, with backtest error, `finwire_hurtcast_pb95`, `finwire_hurtcast_diesel` | `/v1/fuel/retail-estimate` | finwire.pl estimate from ORLEN wholesale prices, [European Commission](https://energy.ec.europa.eu/data-and-analysis/weekly-oil-bulletin_en) (Weekly Oil Bulletin), Minister of Energy (maximum prices); derived numbers only | daily | 104 weeks |
| [Exchange rate forecast](https://public-api.finwire.pl/datasets/currency-forecast) against PLN for 12 currencies (Finwire FXcast): 50% and 80% ranges for the next NBP table to 12 months, forward rate, backtest accuracy, `finwire_fxcast_eur_3m`, `finwire_fxcast_usd_3m`, `finwire_fxcast_gbp_3m`, `finwire_fxcast_chf_3m`, `finwire_fxcast_nok_3m` | `/v1/currencies/forecast` | finwire.pl forecast from [NBP](https://nbp.pl/statystyka-i-sprawozdawczosc/kursy/tabela-a/) mid rates and reference rate, central bank rates from [BIS](https://data.bis.org/topics/CBPOL) | each NBP table A | current forecast, backtest 10 years |
| [Statutory interest rates](https://public-api.finwire.pl/datasets/statutory-interest): statutory, late payment, maximum, tax arrears, commercial (B2B) | `/v1/statutory-interest` | NBP rates and statutory formulas (Civil Code, Tax Ordinance, Late Payments Act) | on NBP rate change | current with next rate |
| [Retail prices in shops](https://public-api.finwire.pl/datasets/retail-prices): 30 goods and services, finwire shopping basket, minutes of work | `/v1/prices/retail` | GUS ([Dziedzinowa Baza Wiedzy](https://dbw.stat.gov.pl/)) | monthly | from 2018 |
| [Business registrations](https://public-api.finwire.pl/datasets/business-registrations): new, closed, suspended and resumed sole proprietorships in CEIDG, share of women, `finwire_new_business_ratio` | `/v1/business-registrations` | [MRiT](https://dane.gov.pl/pl/dataset/745) (dane.gov.pl) | monthly | from November 2019 |
| [New companies](https://public-api.finwire.pl/datasets/new-companies): new commercial companies in KRS by legal form, with new sole proprietorships in CEIDG, `finwire_new_companies_pulse` | `/v1/new-companies` | [Ministry of Justice](https://dane.gov.pl/pl/dataset/27606) (KRS API, dane.gov.pl), [MRiT](https://dane.gov.pl/pl/dataset/745) | monthly, current month daily | 13 months before the first run |
| [Vehicle registrations](https://public-api.finwire.pl/datasets/vehicle-registrations): first registrations by vehicle type and province, passenger-car brands, share of brands from China, `finwire_car_registrations_pulse`, `finwire_chinese_brands_share` | `/v1/vehicle-registrations` | [Ministry of Digital Affairs](https://dane.gov.pl/pl/dataset/440) (CEPiK data, dane.gov.pl) | monthly | from January 2014, brands from January 2023 |
| [Electric cars](https://public-api.finwire.pl/datasets/electric-vehicles): BEV and PHEV fleet and new cars, fuel mix of new cars, counties and provinces, charging vs EV correlation, `finwire_ev_share_new_cars` | `/v1/electric-vehicles` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/road_eqs_carpda/default/table), GUS BDL, UDT EIPA | yearly | Eurostat from 2013 |
| [EV charging points](https://public-api.finwire.pl/datasets/ev-charging): locations, stations and points, fast sites by province and county, `finwire_ev_charging_coverage` | `/v1/ev-charging` | [UDT EIPA](https://eipa.udt.gov.pl/stats) | monthly (month ends), daily current state | from December 2019 |
| [IKE and IKZE retirement accounts](https://public-api.finwire.pl/datasets/ike-ikze): accounts, assets and contributions by institution type, limit usage, `finwire_ike_ikze_avg_balance` | `/v1/ike-ikze` | [KNF](https://www.knf.gov.pl/wyniki_wyszukiwania?searchText=Informacje+o+rynku+IKE) | half-yearly | from June 2020 |
| [Open pension funds (OFE) and the ZUS subaccount](https://public-api.finwire.pl/datasets/ofe): members, net assets and accounting unit by fund, rates of return over 12-120 months, 100 PLN in OFE vs the ZUS subaccount, `finwire_ofe_vs_subaccount_10y` | `/v1/ofe` | [KNF](https://www.knf.gov.pl/wyniki_wyszukiwania?searchText=Dane+miesi%C4%99czne+OFE), [ZUS](https://www.zus.pl/) | monthly, returns half-yearly | from October 2017 (gaps), returns from 2016 |
| [Car insurance: average OC and autocasco premium](https://public-api.finwire.pl/datasets/car-insurance): average premium per policy over 4 quarters and in the quarter, policies in force, claims paid per 100 PLN of premium, 18 non-life classes, `finwire_oc_avg_premium` | `/v1/car-insurance` | [KNF](https://www.knf.gov.pl/publikacje_i_opracowania/dane_statystyczne?categories=%2Fpublikacje_i_opracowania%2Fdane_statystyczne%2Fubepieczeniowy&apply=1) | quarterly | from Q1 2017 |
| [Life insurance: average premium per policy](https://public-api.finwire.pl/datasets/life-insurance): average annual premium per individual contract over 4 quarters for class I and groups 1-5, group premium per insured person, contracts in force, claims and benefits paid per 100 PLN of premium, `finwire_life_avg_premium` | `/v1/life-insurance` | [KNF](https://www.knf.gov.pl/publikacje_i_opracowania/dane_statystyczne?categories=%2Fpublikacje_i_opracowania%2Fdane_statystyczne%2Fubepieczeniowy&apply=1) | quarterly | from Q1 2017 |
| [Investment funds](https://public-api.finwire.pl/datasets/investment-funds): assets and net flows of the market, fund groups, PPK, bond types, equity regions, `finwire_fund_flows_index` | `/v1/investment-funds` | [IZFiA](https://www.izfa.pl/raporty) | monthly | market from February 2020, groups from February 2022 |
| [Czyste Powietrze subsidy programme](https://public-api.finwire.pl/datasets/clean-air): applications, contracts, payouts, payout queue, heat sources, `finwire_clean_air_payout_rate` | `/v1/subsidies/clean-air` | [NFOŚiGW](https://czystepowietrze.gov.pl/efekty-programu/czyste-powietrze-w-liczbach) | weekly | from February 2024 (archived readings) |
| [Average ZUS pension](https://public-api.finwire.pl/datasets/zus-benefits): gross and net, other pensions, allowances, `finwire_pension_wage_ratio` | `/v1/pensions` | [ZUS](https://www.zus.pl/baza-wiedzy/statystyka/miesieczna-informacja-o-wybranych-swiadczeniach-pienieznych), GUS | monthly (preliminary data) | from December 2023 |
| [ZUS pension payment dates](https://public-api.finwire.pl/datasets/pension-payment-dates): payment calendar for 12 months with dates moved before weekends and public holidays, 800+ deadlines, minimum pensions, social pension, minimum pension top-up conditions, supplementary benefit threshold, recipients | `/v1/pension-payment-dates` | [ZUS](https://www.zus.pl/), Act on pensions from FUS (art. 87, art. 130) | daily (calendar), monthly (recipients) | 12 months ahead |
| [Average Pole](https://public-api.finwire.pl/datasets/average-pole) (`finwire_average_pole`): median age, median wage, flat price, average mortgage and model payment, pension, spending, car age, top car brand | `/v1/average-pole` | Eurostat, GUS, BIK, NBP, ZUS, Ministry of Digital Affairs | each trait on its source's release (annual to monthly) | latest readings |
| [Daily price index](https://public-api.finwire.pl/datasets/daily-price-index) (`finwire_daily_price_index`, experimental): online shelf prices of a fixed basket of food, household and personal care products, base month = 100, categories, m/m, food part vs GUS CPI food | `/v1/daily-price-index` | finwire.pl from Frisco.pl, Rossmann.pl, zakupy.biedronka.pl; [GUS](https://dbw.stat.gov.pl/) CPI weights |
| [Pension forecast](https://public-api.finwire.pl/datasets/pension-forecast) (`finwire_pension_forecast`): first ZUS pension as % of the last wage and in today's money by birth year, sex and retirement age and wage level, P10/P50/P90, effect of demography and longer life, OECD and EC benchmarks | `/v1/pension-forecast` | [ZUS](https://www.zus.pl/), [Eurostat](https://ec.europa.eu/eurostat), GUS, Ministry of Finance | recomputed when ZUS indexation, Eurostat or GUS inputs change | birth years 1966-2006, retirement years to 2075 |
| [PPK (Employee Capital Plans)](https://public-api.finwire.pl/datasets/ppk): net assets, active accounts, participants, participation rate, average fee, `finwire_ppk_avg_account` | `/v1/ppk` | [PFR Portal PPK](https://www.mojeppk.pl/pliki-do-pobrania.html) (monthly bulletin) | monthly | from October 2021 |
| [Retail savings bonds vs bank deposits](https://public-api.finwire.pl/datasets/retail-bond-premium): first-year bond rates minus best deposits, savings accounts and average new deposit after tax, `finwire_retail_bond_premium` | `/v1/retail-bond-premium` | Ministry of Finance, NBP, finwire.pl deposit indices | monthly | from the first month with both bond issue and deposit data |
| [Average new deposit and consumer loan rates (NBP MIR)](https://public-api.finwire.pl/datasets/deposit-bank-average): average rate and value of new household deposits by maturity, new consumer loan rate and APRC, best deposits minus bank average `finwire_deposit_choice_premium` | `/v1/deposits/bank-average` | NBP, finwire.pl deposit index | monthly | rates since 2005, deposit values since 2018 |
| [Deposit rate forecast (Finwire Depocast)](https://public-api.finwire.pl/datasets/deposit-forecast): average new deposit rate 1, 3 and 6 months ahead with an 80% interval and backtest accuracy, `finwire_deposit_rate_forecast` | `/v1/deposits/forecast` | NBP, Finwire Nowcast, bank rate forecasts | monthly | NBP average and reference rate history |
| [Average deposit rate since 2005 and the real result of an average deposit](https://public-api.finwire.pl/datasets/deposit-rate-history): average new household deposit rate by maturity monthly, yearly average against annual average CPI (GUS), years above inflation, `finwire_real_avg_deposit_rate` | `/v1/deposits/history` | NBP, GUS | monthly, yearly | since 2005 |
| [Savings bond rate in the next interest period (Finwire Bondcast)](https://public-api.finwire.pl/datasets/bond-next-rate): rate of COI, EDO, ROS, ROD (margin + CPI) and ROR, DOR (margin + NBP reference rate) in the next period from the issue letter rules, state announced, known, estimate or unknown, all periods of one series, `finwire_bond_next_rate` | `/v1/bonds/next-rate` | Ministry of Finance (issue letters, interest tables), GUS, NBP | monthly (EDO, COI, ROS, ROD), each month (ROR, DOR) | index history: Ministry of Finance rates of the 2nd year of EDO |
| [Household fixed cost burden](https://public-api.finwire.pl/datasets/fixed-cost-burden): mortgage, admin fee, utilities and TV licence as % of net income, Poland and 16 provinces, `finwire_fixed_cost_burden` | `/v1/fixed-cost-burden` | GUS, NBP, KRRiT | monthly | last 36 months with complete inputs |
| [Heating season](https://public-api.finwire.pl/datasets/heating-season): degree days since 1 July vs the 20-season norm, cold index, season cost of a 100 m2 house, Poland and 17 cities, `finwire_heating_season_index`, `finwire_heating_season_cost` | `/v1/heating-season` | IMGW-PIB, Eurostat, URE, GUS | daily | seasons since 1966/67 |
| [Polish public debt and state budget](https://public-api.finwire.pl/datasets/public-debt): State Treasury debt, debt per resident `finwire_public_debt_per_capita`, public debt % of GDP, budget deficit | `/v1/public-debt` | [Ministry of Finance](https://www.gov.pl/web/finanse/szeregiczasowe), GUS, Eurostat | monthly (debt), quarterly (% of GDP), monthly (budget estimate) | debt from January 2001, budget from 2018 |
| [PIT returns by municipality](https://public-api.finwire.pl/datasets/pit-returns): PIT due per resident `finwire_pit_per_capita`, by type of return (PIT-37, PIT-36L, PIT-28, PIT-38), every municipality and province | `/v1/pit/summary`, `/v1/pit/municipalities`, `/v1/pit/municipalities/{teryt}` | [Ministry of Finance](https://dane.gov.pl/pl/dataset/3497), GUS | yearly | tax years from 2022 |
| [Regulated electricity and gas prices](https://public-api.finwire.pl/datasets/energy-tariffs): household tariffs G11, G12, G12w and W-1.1 to W-4 | `/v1/energy/regulated-prices` | [President of URE](https://bip.ure.gov.pl/bip/taryfy-i-inne-decyzje-b) (tariff decisions) | on new decision | tariff years in force |
| [Brokerage account fees](https://public-api.finwire.pl/datasets/broker-fees): commissions on GPW, US and German stocks, account and FX fees | `/v1/brokers` | brokers' official fee tables, compiled by finwire.pl | weekly check | current fee tables |
| [Legal changes radar](https://public-api.finwire.pl/datasets/legal-changes): acts entering into force, bills in the Sejm | `/v1/legal-changes` | [Sejm ELI API](https://api.sejm.gov.pl/eli.html) | daily | upcoming and recent |
| [Acts awaiting the President's decision](https://public-api.finwire.pl/datasets/bills-president-desk): deadline (art. 122 ust. 2 of the Constitution), days left, recent signatures and vetoes | `/v1/legal-changes/president-desk` | [Sejm API](https://api.sejm.gov.pl/sejm.html), [prezydent.pl](https://www.prezydent.pl) | on change | current and last 30 days |
| [Residents income by municipality and county](https://public-api.finwire.pl/datasets/municipal-income) | `/v1/income/municipalities` | Ministry of Finance (Rb-27S), [GUS BDL](https://bdl.stat.gov.pl), computed by finwire.pl | yearly, quarterly update | budget years |
| [Coal price and waste fee](https://public-api.finwire.pl/datasets/coal-waste-prices) by province (`finwire_coal_price_pl`, `finwire_waste_fee_pl`) | `/v1/coal-waste-prices` | [GUS BDL](https://bdl.stat.gov.pl) | yearly | 2020 onwards |
| [Housing running costs](https://public-api.finwire.pl/datasets/housing-upkeep) in 20 cities | `/v1/housing/upkeep/{city}` | [GUS BDL](https://bdl.stat.gov.pl) | yearly | latest year |
| [House prices and housing conditions in Europe](https://public-api.finwire.pl/datasets/eu-housing) | `/v1/housing/europe` | [Eurostat](https://ec.europa.eu/eurostat) | quarterly, yearly | since 2010 (conditions) |
| [Bank lending survey](https://public-api.finwire.pl/datasets/credit-survey) | `/v1/credit-market/survey` | [NBP](https://nbp.pl) | quarterly | editions |
| [Household loans and deposits](https://public-api.finwire.pl/datasets/nbp-monetary) | `/v1/credit-market/monetary` | [NBP](https://nbp.pl) | monthly | up to 400 months |
| [Banking sector: profit, loans and deposits](https://public-api.finwire.pl/datasets/banking-sector): net profit year-to-date, in the month and over 12 months, tax, costs, housing and consumer loans with impaired share, household deposits, `finwire_bank_interest_share` | `/v1/credit-market/banking-sector` | [KNF](https://www.knf.gov.pl/?articleId=56224&p_id=18) | monthly | from January 2018 |
| [Housing loan demand (BIK)](https://public-api.finwire.pl/datasets/bik-credit-demand) | `/v1/credit-market/demand` | [BIK](https://media.bik.pl/informacje-prasowe), [NBP](https://nbp.pl) | monthly | from 2025-08 |
| [Mortgage payment risk and fixed-rate premium](https://public-api.finwire.pl/datasets/rate-risk): chance the variable-rate payment rises by more than 5% in 12 months, payment percentiles in 3, 6 and 12 months, backtest accuracy, `finwire_rate_risk_gauge`, `finwire_fixed_rate_premium` | `/v1/credit-market/rate-risk` | finwire.pl model on [GPW Benchmark](https://gpwbenchmark.pl) WIBOR (result only), bank offers, [ECB MIR](https://data.ecb.europa.eu/data/datasets/MIR) | daily | month ends from 2008 |
| [Interest rates on new loans in Europe](https://public-api.finwire.pl/datasets/ecb-mortgage-rates) | `/v1/credit-market/ecb-rates` | [ECB MIR](https://data.ecb.europa.eu) | monthly | since 2014 |
| [Electricity and gas prices for households in the EU](https://public-api.finwire.pl/datasets/eu-energy-prices): EUR, PPS, PLN, `finwire_eu_power_price_gap`, kWh per minimum wage | `/v1/europe/energy-prices` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/nrg_pc_204/default/table) (nrg_pc_204, nrg_pc_202) | half-yearly | since 2007 |
| [Fuel prices in EU countries](https://public-api.finwire.pl/datasets/eu-fuel-prices): petrol 95, diesel, LPG, heating oil in EUR and PLN per litre, taxes, rank, `finwire_eu_fuel_price_gap` | `/v1/europe/fuel-prices` | [European Commission](https://energy.ec.europa.eu/data-and-analysis/weekly-oil-bulletin_en) (Weekly Oil Bulletin), NBP | weekly | since 2005 |
| [GDP per capita in the EU](https://public-api.finwire.pl/datasets/eu-gdp-per-capita): PPS (EU27 = 100) and EUR per inhabitant, real growth, AIC consumption, price level, latest quarter GDP growth, `finwire_eu_convergence_years` | `/v1/europe/gdp-per-capita` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/nama_10_pc/default/table) (nama_10_pc, prc_ppp_ind_1, namq_10_gdp) | yearly and quarterly | since 1995 |
| [Minimum wages in the EU](https://public-api.finwire.pl/datasets/eu-minimum-wages): EUR, PPS, national currency | `/v1/europe/minimum-wages` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/earn_mw_cur/default/table) (earn_mw_cur) | half-yearly | since 1999 |
| [Inflation (HICP) in EU countries](https://public-api.finwire.pl/datasets/eu-inflation): ranking, Poland, core, food, energy, services, basket since a year, `finwire_eu_inflation_gap` | `/v1/europe/inflation` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/prc_hicp_minr/default/table) |
| [Unemployment in EU countries](https://public-api.finwire.pl/datasets/eu-unemployment): total and under 25, rank, Poland rank over time, `finwire_eu_youth_unemployment_ratio` | `/v1/europe/unemployment` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/une_rt_m/default/table) (une_rt_m) | monthly | since 2010 |
| [Net earnings in the EU by household type](https://public-api.finwire.pl/datasets/eu-net-earnings): EUR, PPS, gross, tax, contributions, labour cost per hour, tax wedge, `finwire_eu_net_wage_gap`, own salary vs every EU country | `/v1/europe/net-earnings` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/earn_nt_net/default/table) (earn_nt_net, lc_lci_lev, earn_nt_taxwedge) | yearly | since 2010 |
| [Government debt and deficit in the EU](https://public-api.finwire.pl/datasets/eu-public-debt): % of GDP, ranks, 3% and 60% reference values, `finwire_eu_debt_threshold_gap` | `/v1/europe/public-debt` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/gov_10dd_edpt1/default/table) (gov_10dd_edpt1, gov_10q_ggdebt) | yearly, quarterly | since 2000 (yearly), 2010 (quarterly) |
| [Living conditions in the EU (EU-SILC)](https://public-api.finwire.pl/datasets/eu-living-conditions): own home with and without a mortgage, renting, people who cannot face an unexpected expense, median and mean household income per person in EUR and PPS, `finwire_eu_mortgaged_owners`, `finwire_eu_household_income_gap` | `/v1/europe/living-conditions` | [Eurostat](https://ec.europa.eu/eurostat/databrowser/view/ilc_lvho02/default/table) (ilc_lvho02, ilc_mdes04, ilc_di03) | yearly | since 2005 |
| [Household and retiree inflation in Poland](https://public-api.finwire.pl/datasets/household-inflation): Statistics Poland CPI for households of retirees and pensioners vs total, year-to-date and annual averages, the higher index under art. 89(2) of the pension act, basket shares; `finwire_household_inflation_index` for family types and income quintiles | `/v1/household-inflation` | [GUS](https://stat.gov.pl/obszary-tematyczne/ceny-handel/wskazniki-cen/) | monthly | since January 2018 |
| [Household savings in Poland](https://public-api.finwire.pl/datasets/household-savings): financial assets of households by instrument (cash, deposits, bonds, equity, funds, insurance, pension), `finwire_household_invested_share`, Poland vs OECD countries | `/v1/household-savings` | [ECB QSA](https://data.ecb.europa.eu/data/datasets/QSA) (NBP data), [OECD](https://data-explorer.oecd.org/) (DF_FIN_DASH_S1M) | quarterly (OECD yearly) | since Q4 2003 |
| [Forecasts for Poland](https://public-api.finwire.pl/datasets/poland-forecasts): European Commission (AMECO), OECD, NBP and Finwire Nowcast editions, GDP, inflation, unemployment, deficit and debt, forecast errors, `finwire_forecast_accuracy` | `/v1/forecasts/poland` | European Commission, OECD, NBP, GUS |
| [Poland vs OECD countries](https://public-api.finwire.pl/datasets/oecd-comparison): pension replacement rates (net and gross, men and women), tax wedge for 6 household profiles also outside the EU, `finwire_oecd_pension_gap`, `finwire_oecd_tax_wedge_gap` | `/v1/oecd/comparison` | [OECD](https://data-explorer.oecd.org/) (DSD_PAG@DF_PRR, DSD_TAX_WAGES_COMP@DF_TW_COMP) | yearly | pensions: latest edition; tax wedge: since 2000 |
| [Tourism pressure in municipalities](https://public-api.finwire.pl/datasets/tourism): bed places per 1000 residents with the municipality ranking, nights and tourists by county and province, bed occupancy by month, `finwire_tourism_pressure` | `/v1/tourism` | [GUS BDL](https://bdl.stat.gov.pl) (P2017, P3186, P2882) | yearly, monthly | 12 years (Poland, provinces), 2 years (counties, municipalities) |
| [Average pension: ZUS vs KRUS](https://public-api.finwire.pl/datasets/pension-systems): yearly, year to date, provinces | `/v1/pension-systems` | [GUS BDL](https://bdl.stat.gov.pl) (P2860, P2503) | yearly, quarterly | since 1999 |
| [Economic calendar](https://public-api.finwire.pl/datasets/economic-calendar): MPC (RPP) decisions, CPI, wages, GDP, unemployment | `/v1/calendar` | GUS, NBP, compiled by finwire.pl | continuous | upcoming events |
| [IKE / IKZE contribution limits](https://public-api.finwire.pl/datasets/legal-limits) | `/v1/legal-limits` | Ministry of Family announcements | yearly | current year |
| [Voluntary NFZ health insurance contribution](https://public-api.finwire.pl/datasets/legal-limits) (block `voluntaryHealth`) | `/v1/legal-limits` | Prezes GUS announcements in Monitor Polski (art. 5 pkt 31 of the health care services act) | quarterly | current and next quarter |
| [Property tax upper limits](https://public-api.finwire.pl/datasets/local-tax) | `/v1/legal-limits/local-tax` | Minister of Finance ([Monitor Polski](https://eli.gov.pl)) | yearly | announced tax years |
| [Deposit](https://public-api.finwire.pl/datasets/deposit-rates), [savings](https://public-api.finwire.pl/datasets/savings-rates), [mortgage](https://public-api.finwire.pl/datasets/mortgage-rates) and [cash loan](https://public-api.finwire.pl/datasets/cash-loan-rates) rate aggregates | `/v1/market/*` | finwire.pl (aggregate of offers on the market) | daily | current |
| [finwire index registry](https://public-api.finwire.pl/datasets/finwire-index): every index computed by finwire.pl under a stable `finwire_*` id ([list](#finwire-indices-finwire_-ids)) | `/v1/finwire-index/{id}` | finwire.pl (method and sources per index) | daily, monthly or quarterly per index | per index |
| [Legal calendar](https://public-api.finwire.pl/datasets/legal-calendar) (`finwire_legal_calendar`): PIT, ZUS and VAT deadlines, IKE/IKZE year end, changes of statutory values, acts entering into force, RPP meetings, retail bond sales | `/v1/legal-calendar` | [Sejm ELI API](https://api.sejm.gov.pl/eli.html), NBP, Ministry of Finance, statutory deadlines, compiled by finwire.pl | daily | upcoming events, ICS at [finwire.pl/kalendarz-zmian.ics](https://finwire.pl/kalendarz-zmian.ics) |
| [KNF public warnings](https://public-api.finwire.pl/datasets/knf-warnings): entries verbatim from KNF, counts by legal basis, entity lookup, weekly new entries (`finwire_knf_warnings_monitor`) | `/v1/knf-warnings` | [KNF, lista ostrzeżeń publicznych](https://www.knf.gov.pl/dla_konsumenta/ostrzezenia_publiczne) | 3 times a day | current list, weekly series since November 2026 |
| [BFG deposit guarantee list](https://public-api.finwire.pl/datasets/bfg-guarantee): banks and credit unions covered by the BFG guarantee, counts by group with history (`finwire_bfg_guarantee_monitor`), latest changes, lookup by name or website | `/v1/bfg-guarantee` | [BFG, lista podmiotów objętych gwarancjami](https://bfg.pl/gwarantowanie-depozytow/podmioty-objete-gwarancjami/) | twice a day | every change of the list since 2019 |
| [UOKiK and KNF fines of banks](https://public-api.finwire.pl/datasets/bank-penalties): fines of banks with a finwire.pl page from the last 5 years, count and sum per bank (`finwire_bank_penalty_count`), fines of one bank with links to the decisions | `/v1/bank-penalties` | [KNF, wykaz kar](https://www.knf.gov.pl/o_nas/Kary_nalozone_przez_KNF), [UOKiK, komunikaty](https://uokik.gov.pl/aktualnosci) | daily | KNF lists from the last 5 years; UOKiK from the first press release read |
| [Court rulings in credit disputes](https://public-api.finwire.pl/datasets/credit-judgments): rulings of common courts on CHF and other currency mortgages, WIBOR, free credit sanction, early repayment refunds and unit-linked policies, monthly counts, share for the borrower (`finwire_borrower_win_share`), instances, newest rulings with links | `/v1/credit-judgments` | [Court Rulings Portal, Ministry of Justice](https://orzeczenia.ms.gov.pl) via [SAOS](https://www.saos.org.pl) | daily | all published rulings, history filled over the first weeks |
| [Tax rulings](https://public-api.finwire.pl/datasets/tax-rulings): KIS and municipal individual rulings by personal finance topic, weekly counts (`finwire_tax_rulings_pulse`), most cited provisions, newest rulings with links | `/v1/tax-rulings` | [Ministry of Finance, EUREKA](https://eureka.mf.gov.pl) | daily | last 53 weeks, growing |
| [Next-year statutory limits](https://public-api.finwire.pl/datasets/next-year-limits) (`finwire_next_year_limits`): pension indexation, minimum wage, IKE/IKZE limits, ZUS caps and bases, flat-rate tax and small CIT taxpayer limits for the next year from the formulas in law, with stage, 80% interval and backtest | `/v1/legal-limits/next-year` | finwire.pl calculation from GUS and NBP data and acts in the Journal of Laws and Monitor Polski | several times a day | next year, estimate path since June, backtest on past years |
| [Cost of living in cities](https://public-api.finwire.pl/datasets/cost-of-living) (`finwire_city_cost_of_living`): monthly cost of a family of 2 adults and 1 child renting or owning a 3-room flat in 17 cities - rent, running costs, property tax, household budget expenses, flat price | `/v1/cost-of-living` | znajdznajem.pl (CC BY 4.0), GUS, RCN, Minister of Finance | daily | latest |
| [Local fees and transport tax](https://public-api.finwire.pl/datasets/local-fees) (`finwire_resort_fee_family_week`): upper limits of local fees and the transport vehicle tax from the Minister of Finance announcements, tourist and spa fee per night from 2027, agricultural and forest tax per hectare | `/v1/local-fees` | Minister of Finance and President of GUS (Monitor Polski), act Dz.U. 2026 poz. 1354 | yearly | tax years since 2019 |
| [Next-year property tax limits](https://public-api.finwire.pl/datasets/local-tax-next-year) (`finwire_property_tax_cap`): upper limits of the local property tax per m² for the next year from the formula in art. 20 of the local taxes act (this year limit x GUS half-year CPI index), estimate before the GUS notice and the Minister of Finance announcement, all 9 categories, accuracy on past years | `/v1/legal-limits/local-tax/next-year` | finwire.pl calculation from GUS CPI and Minister of Finance announcements in Monitor Polski | daily | next year, estimate path from the first CPI reading of the half-year, accuracy since 2020 |
| [Entry threshold](https://public-api.finwire.pl/datasets/entry-threshold): wage needed for a mortgage on a model flat, 17 cities | `/v1/index/entry-threshold` | finwire.pl (prices from notarial deeds in RCN, GUS RCiWN fallback, mortgage offers, GUS wage distribution) | daily | since September 2026 |
| [Finwire Nowcast](https://public-api.finwire.pl/datasets/nowcast): forecasts of CPI and core inflation and the RPP decision | `/v1/macro/nowcast` | finwire.pl model (inputs: GUS, NBP, EC Oil Bulletin) | daily, frozen 3 working days before publication | backtest since 2019 (CPI, core), 2012 (RPP) |
| [finwire lead forecasts](https://public-api.finwire.pl/datasets/lead-forecasts): core inflation in 6-12 months and impaired loans in 2-4 quarters | `/v1/macro/lead-forecasts` | finwire.pl models (inputs: GUS, NBP, Eurostat, KNF) | daily model run, monthly data | rolling backtest, published only above 70% interval coverage |
| finwire indicators: [credit stress map](https://public-api.finwire.pl/datasets/credit-stress), barometers | `/v1/index/*` | finwire.pl (computed from the above) | daily (credit stress), monthly (barometers) | barometers since 2015 |
| [Consumer bankruptcies](https://public-api.finwire.pl/datasets/consumer-bankruptcies): people declared bankrupt as consumers per month, year on year, since January, rank in the series, yearly totals, `finwire_consumer_bankruptcies` | `/v1/consumer-bankruptcies` | [COIG](https://www.coig.com.pl/) (from announcements in KRZ and MSiG; reuse with attribution to COIG, not CC BY) | monthly | from January 2015 |
| [PIT settlement in Poland](https://public-api.finwire.pl/datasets/pit-national): scale taxpayers and returns by form, taxpayers in each bracket and their share in tax due, reliefs (IKZE, child relief, thermal modernisation and more), overpayments, flat tax, lump-sum tax, `finwire_pit_top_bracket_multiplier` | `/v1/pit/national` | [Ministry of Finance](https://podatki.gov.pl/poradniki-i-informatory/informacja-dotyczaca-rozliczenia-podatku-dochodowego-od-osob-fizycznych) (yearly information; the same series on dane.gov.pl is CC0 1.0) | yearly | tax years from 2019 |
| [Parking space prices from developers](https://public-api.finwire.pl/datasets/developer-parking-prices): median asking price of one parking space per city and week, underground vs outdoor vs garage, storage rooms, bike boxes, square metres of a new flat a space costs, `finwire_dev_parking_price` | `/v1/developer-parking-prices` | [dane.gov.pl](https://dane.gov.pl/pl/institution?type=developer) (developer price lists, CC0) | weekly | from the first week of the series |
| [Family housing loan price limits](https://public-api.finwire.pl/datasets/rkm-price-limits): maximum price per m2 for a mortgage without down payment with a BGK guarantee, 52 locations, primary and secondary market, `finwire_rkm_offer_fit` | `/v1/rkm-price-limits` | [BGK](https://www.bgk.pl/produkty/rodzinny-kredyt-mieszkaniowy/) (from voivode announcements; no reuse terms stated by BGK, cite with attribution, not CC BY) | quarterly | from Q1 2023 |
| [House building cost](https://public-api.finwire.pl/datasets/build-cost): replacement cost per m2 of a residential building announced by voivodes in 52 locations, a 120 m2 house per voivodeship, GUS construction and assembly price indices, `finwire_house_build_cost` | `/v1/build-cost` | Voivode announcements (official documents) via [BGK](https://www.bgk.pl/produkty/rodzinny-kredyt-mieszkaniowy/) price-limit lists (cite with attribution, not CC BY); [GUS](https://dbw.stat.gov.pl/) (CC BY 4.0) | quarterly (replacement cost), monthly (GUS) | from Q1 2023 (replacement cost), from 2010 (GUS) |
| [Cumulative inflation](https://public-api.finwire.pl/datasets/cumulative-inflation): how much consumer prices rose between any two months or years since 1982, chained GUS indices (December on December for full years, month on month within a year), every year and the cumulative inflation since January of each year | `/v1/cumulative-inflation` | [GUS](https://stat.gov.pl/obszary-tematyczne/ceny-handel/wskazniki-cen/) (CC BY 4.0) | monthly | from January 1982 |
| [Parental leave planner](https://public-api.finwire.pl/datasets/parental-leave-plan): dates of maternity, parental and paternity leave of both parents from the birth date, split of weeks, maternity benefit per period, per month and in total from gross pay, 81.5% on request vs 100% and 70%, deadlines, non-transferable weeks a parent loses | `/v1/parental-leave-plan` | [Kodeks pracy](https://api.sejm.gov.pl/eli/acts/DU/2026/1245/text.pdf) art. 180-183, [sickness and maternity benefits act](https://api.sejm.gov.pl/eli/acts/DU/2026/854/text.pdf) art. 29a-31, computed by finwire.pl | computed on request | rules in force now |
| [Start-up grants and labour office support](https://public-api.finwire.pl/datasets/labour-office-support): labour office start-up grant, PFRON grant, social cooperative grant, start-up loan, education loan and local action group premium with amounts from the average wage in force, sickness benefit after the end of employment check | `/v1/labour-office-support` | [Act on the labour market](https://api.sejm.gov.pl/eli/acts/DU/2025/620/text.pdf) art. 111, 147-153, 161, 172-174, 187, [Act on vocational rehabilitation](https://api.sejm.gov.pl/eli/acts/DU/2026/884/text.pdf) art. 12a, [sickness benefits act](https://api.sejm.gov.pl/eli/acts/DU/2026/854/text.pdf) art. 7, 8, 13, GUS average wage, computed by finwire.pl | quarterly (GUS average wage), computed on request | rules in force now |
| [Cash loan prepayment](https://public-api.finwire.pl/datasets/cash-loan-prepayment): interest with a shorter term vs a lower instalment, early repayment fee under the Consumer Credit Act art. 50 with the fee-free threshold, refund of up-front costs under art. 49, net saving | `/v1/cash-loan-prepayment` | [Consumer Credit Act](https://api.sejm.gov.pl/eli/acts/DU/2025/1362/text.pdf) art. 48-50, NBP interest rate statistics (MIR), GUS average wage, computed by finwire.pl | computed on request | rules in force now |
| [Child benefits paid by ZUS](https://public-api.finwire.pl/datasets/family-benefits): 800 plus and the three Aktywny Rodzic benefits per month, amounts since January and in the month, 800 plus applications, voivodeships, `finwire_child_benefits_payout` | `/v1/family-benefits` | [ZUS Portal Statystyczny](https://psz.zus.pl/kategorie/programy-wsparcia-dla-rodzin) (CC0 1.0 on dane.gov.pl) | monthly | from January 2024 (Aktywny Rodzic from December 2024) |
| [Savings bond sales](https://public-api.finwire.pl/datasets/savings-bond-sales): retail savings bonds sold per month, series, exchange, IKE/IKZE, channels, buyer age, since January vs every year, `finwire_savings_bond_sales` | `/v1/savings-bond-sales` | [Ministry of Finance](https://www.gov.pl/web/finanse/wiadomosci) (gov.pl, reuse with attribution) | monthly | from January 2015 |
| [Central bank rates worldwide](https://public-api.finwire.pl/datasets/world-policy-rates): ranking, real rates, place of the NBP rate `finwire_policy_rate_rank`, REER of the zloty | `/v1/world/policy-rates` | [BIS](https://data.bis.org/topics/CBPOL) (WS_CBPOL, WS_LONG_CPI, WS_EER) | daily and monthly | rank since 2000 |
| [House prices worldwide](https://public-api.finwire.pl/datasets/world-house-prices): real and nominal change by country, price to income and rent | `/v1/world/house-prices` | [BIS](https://data.bis.org/topics/RPP) (WS_SPP), [OECD](https://data-explorer.oecd.org/) (DF_HOUSE_PRICES) | quarterly | Poland since 2010 (BIS), 2005 (OECD) |
| [Credit gap and debt service ratio of Poland](https://public-api.finwire.pl/datasets/world-credit-cycle) | `/v1/world/credit-cycle` | [BIS](https://data.bis.org/topics/CREDIT_GAPS) (WS_CREDIT_GAP, WS_DSR) | quarterly | full BIS history |
| [Finwire Housecast](https://public-api.finwire.pl/datasets/housecast): housing valuation gap, real house price forecast 4 and 8 quarters ahead with backtest, `finwire_housing_valuation_gap` | `/v1/world/housecast` | finwire.pl model on [OECD](https://data-explorer.oecd.org/) and [BIS](https://data.bis.org/topics/RPP) | quarterly | gap since 2015 |
| [Car costs](https://public-api.finwire.pl/datasets/car-costs): cost of driving 100 km with petrol, diesel, LPG and electricity, legal constants of buying and importing a car, toll motorways, speeding fines, `finwire_car_cost_100km` | `/v1/car-costs` | Minister of Energy (Monitor Polski), [European Commission Weekly Oil Bulletin](https://energy.ec.europa.eu/data-and-analysis/weekly-oil-bulletin_en), [UDT EIPA](https://eipa.udt.gov.pl/), URE, ICCT, NBP, TARIC, Dziennik Ustaw | daily | last 400 days |
| [US economy](https://public-api.finwire.pl/datasets/us-economy): CPI and core CPI with Polish CPI, jobs report, Treasury par yield curve, public debt, `finwire_us_debt_per_second`, `finwire_us_real_wage_growth` | `/v1/us-economy` | [BLS](https://www.bls.gov/) (CPI-U, CES, CPS), [US Treasury](https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve) (par yield curve), [Fiscal Data](https://fiscaldata.treasury.gov/datasets/debt-to-the-penny/) (Debt to the Penny); public domain | monthly, daily | since 2010 |

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
- Rates and prices: `get_interest_rates` (NBP), `get_statutory_interest` (statutory, tax and B2B interest), `get_currency_rates` (NBP FX, gold, gold by fineness and ounce in USD with `fineness=true`), `get_fuel_prices` (maximum fuel prices and crude oil Brent and WTI in USD and PLN, EIA), `get_energy_prices` (URE electricity and gas tariffs), `get_retail_prices` (GUS shop prices, finwire shopping basket, excise duty in prices of cigarettes, alcohol and fuel, `finwire_cigarette_excise_share`)
- Pump prices tomorrow: `get_fuel_retail_estimate` (finwire estimate of the average petrol 95 and diesel price at Polish stations from wholesale prices, with backtest error, `finwire_hurtcast_pb95`, `finwire_hurtcast_diesel`)
- Exchange rate forecast: `get_currency_forecast` (Finwire FXcast: 50% and 80% ranges of the NBP rate against PLN for the next NBP table to 12 months, point, forward rate, backtest accuracy, `finwire_fxcast_*_3m`)
- Subsidies: `get_subsidy_stats` (Czyste Powietrze heating subsidy programme: applications, contracts, payouts, payout queue, `finwire_clean_air_payout_rate`)
- Pensions: `get_pension_stats` (average ZUS pension gross and net, other pensions, allowances, `finwire_pension_wage_ratio`, ZUS pension payment dates)
- Typical person: `get_average_pole` (Average Pole `finwire_average_pole`: median age, wage, flat, mortgage and model payment, pension, spending, car age, top brand; each trait with measure and period)
- Online prices: `get_daily_price_index` (experimental Daily price index `finwire_daily_price_index`: online shelf prices of a fixed basket, base month = 100, categories, largest rise and fall, food part vs GUS CPI food, the index per shop with category changes next to GUS CPI; no single product prices)
- Pension forecast: `get_pension_forecast` (model of the future ZUS pension `finwire_pension_forecast`: replacement rate and pension in today's money by birth year 1966-2006, profile k60/k65/m65 and wage level, P10/P50/P90, effect of the shrinking workforce and longer life, OECD and EC benchmarks)
- Businesses: `get_business_registrations` (applications in CEIDG to register, close, suspend and resume a sole proprietorship, `finwire_new_business_ratio`), `get_new_companies` (new commercial companies in KRS by legal form with new sole proprietorships, `finwire_new_companies_pulse`)
- Cars: `get_vehicle_registrations` (first registrations by type, province and passenger-car brand, share of brands from China, `finwire_car_registrations_pulse`, `finwire_chinese_brands_share`), `get_electric_vehicles` (BEV and PHEV fleet and new cars, counties, charging vs EV correlation, `finwire_ev_share_new_cars`), `get_ev_charging` (EIPA charging points, fast sites, `finwire_ev_charging_coverage`, AFIR power classes with median prices), `find_ev_chargers` (chargers near a city or point: power, free now, price per kWh, one site card), growth of charging infrastructure in `get_ev_charging` (`finwire_ev_infrastructure_growth`), `get_ev_charging_prices` (price per kWh by power class and network, price changes, `finwire_ev_charging_price_index`)
- PPK: `get_ppk_stats` (Employee Capital Plans from the PFR Portal PPK bulletin: net assets, active accounts, participants, participation rate, average fee, `finwire_ppk_avg_account`)
- Retail bonds vs deposits: `get_retail_bond_premium` (first-year premium of each retail savings bond series over the best deposits, savings accounts and the average new deposit after tax, retail bond sales, `finwire_retail_bond_premium`)
- Bank deposit averages: `get_deposit_bank_average` (average rate on new deposits by maturity and new consumer loan rate and APRC from NBP MIR, best deposits minus bank average after tax, `finwire_deposit_choice_premium`; `view=history`: average since 2005 and the real result of an average deposit year by year, `finwire_real_avg_deposit_rate`)
- Deposit rate forecast: `get_deposit_forecast` (Finwire Depocast `finwire_deposit_rate_forecast`: average new deposit rate 1, 3 and 6 months ahead with an 80% interval and backtest accuracy, only horizons that passed the gate)
- Next bond rate: `get_bond_next_rate` (Finwire Bondcast `finwire_bond_next_rate`: rate of a savings bond series in the next interest period from the issue letter rules, state announced, known, estimate with 80% interval or RPP scenarios, or unknown with the date it will be known; by series code or by type and purchase month)
- Household budget: `get_fixed_cost_burden` (share of net income taken by mortgage, admin fee, utilities and TV licence, Poland and 16 provinces, `finwire_fixed_cost_burden`)
- Heating season: `get_heating_season` (degree days vs norm, cold index, season forecast and heating cost of a 100 m2 house, Poland and 17 cities, `finwire_heating_season_index`, `finwire_heating_season_cost`)
- Investment funds: `get_investment_funds` (IZFiA assets, inflows, outflows and net flows by fund group, PPK, `finwire_fund_flows_index`)
- IKE and IKZE: `get_ike_ikze_stats` (KNF half-yearly accounts, assets and contributions by institution type, limit usage, `finwire_ike_ikze_avg_balance`)
- OFE: `get_ofe_stats` (KNF monthly members, net assets and accounting unit by fund, rates of return over 12-120 months, 100 PLN in OFE vs the ZUS subaccount, `finwire_ofe_vs_subaccount_10y`)
- Net earnings in the EU: `get_eu_earnings` (Eurostat net pay by household type in EUR and PPS, labour cost per hour, tax wedge, a net salary in PLN compared with every EU country, `finwire_eu_net_wage_gap`)
- Car insurance: `get_car_insurance_market` (KNF quarterly average OC and autocasco premium per policy, policies in force, claims paid per 100 PLN of premium, 18 non-life classes, `finwire_oc_avg_premium`)
- Life insurance: `get_life_insurance_market` (KNF quarterly average annual premium per individual life policy, class I and groups 1-5, group premium per insured person, contracts in force, claims and benefits paid per 100 PLN of premium, `finwire_life_avg_premium`)
- Banking sector: `get_banking_sector` (KNF monthly net profit of banks year-to-date, in the month and over 12 months, tax, costs, impaired housing and consumer loans, household deposits, `finwire_bank_interest_share`)
- Fuel prices in the EU: `get_eu_fuel_prices` (European Commission Weekly Oil Bulletin: petrol 95, diesel, LPG and heating oil in PLN and EUR per litre in 27 EU countries, rank, taxes, full-tank difference against Poland, `finwire_eu_fuel_price_gap`)
- Inflation in the EU: `get_eu_inflation` (Eurostat HICP in 27 EU countries: ranking, place of Poland, components, price growth since a year, `finwire_eu_inflation_gap`).
- Unemployment in the EU: `get_eu_unemployment` (Eurostat monthly unemployment rate, total and under 25, rank of Poland since 2010, `finwire_eu_youth_unemployment_ratio`)
- Living conditions in the EU: `get_eu_living_conditions` (Eurostat EU-SILC: own home with and without a mortgage, renting, people who cannot face an unexpected expense, median household income per person in 27 EU countries, `finwire_eu_mortgaged_owners`, `finwire_eu_household_income_gap`)
- Forecasts for Poland: `get_poland_forecasts` (European Commission, OECD, NBP and Finwire Nowcast: GDP, inflation, unemployment, deficit and debt for this and next year, previous edition, errors of past forecasts, `finwire_forecast_accuracy`)
- Poland against the world: `get_world_comparison` (BIS and OECD: central bank rates and the place of the NBP rate `finwire_policy_rate_rank`, real house prices, price to income and rent, credit gap, Finwire Housecast `finwire_housing_valuation_gap`)
- Poland vs OECD: `get_oecd_comparison` (OECD Pensions at a Glance replacement rates and Taxing Wages tax wedge, also countries outside the EU, `finwire_oecd_pension_gap`, `finwire_oecd_tax_wedge_gap`)
- Tourism in municipalities: `get_tourism_pressure` (GUS BDL: bed places per 1000 residents and the municipality ranking, nights and tourists per county and province, bed occupancy by month, `finwire_tourism_pressure`)
- ZUS vs KRUS: `get_pension_systems` (GUS BDL: average ZUS pension and average KRUS farmer pension, yearly since 1999, year to date, provinces)
- Car costs: `get_car_costs` (cost of 100 km and of a trip with petrol, diesel, LPG and electricity, PCC on a used car, customs duty with the US-origin preference, excise and import VAT order, toll motorways, speeding fines and penalty points, `finwire_car_cost_100km`)
- US economy: `get_us_economy` (BLS and US Treasury: CPI and core CPI next to Polish CPI, jobs report, Treasury yield curve with spreads and inverted days, public debt per second in USD and PLN, `finwire_us_debt_per_second`, `finwire_us_real_wage_growth`)
- Public debt: `get_public_debt` (State Treasury debt, debt per resident `finwire_public_debt_per_capita`, public debt % of GDP, state budget deficit)
- finwire indices: `list_finwire_indices` (all `finwire_*` ids with the latest reading), `get_finwire_index` (one index by id or alias, with history and citation)
- Forecasts: `get_nowcast` (Finwire Nowcast: CPI and core inflation, RPP decision probabilities, accuracy)
- Lead forecasts: `get_lead_forecasts` (core inflation in 6-12 months `finwire_cost_pressure`, impaired housing and consumer loans in 2-4 quarters `finwire_npl_forecast`; shown only above the accuracy threshold)
- Housing: `get_housing_prices` (notarial deeds, cities and districts), `get_rents` (rents, rental yield), `get_housing_costs`, `get_cost_of_living` (monthly cost of a family of 2+1 per city, renting or owning, `finwire_city_cost_of_living`), `get_coal_waste_prices` (annual coal price and waste fee, Poland and 16 provinces), `get_housing_balance` (`finwire_housing_pressure` new residents per new dwelling, `finwire_housing_pipeline` completions forecast from building permits, `finwire_rent_forecast` 6-month rent forecast)
- Credit, savings and investing: `get_credit_market`, `get_rate_risk` (mortgage payment risk in 3, 6 and 12 months with backtest, `finwire_rate_risk_gauge`; fixed-rate premium `finwire_fixed_rate_premium`), `get_bank_rates` (aggregates only, no single offers; personal account costs by group; retail bonds), `get_broker_fees` (brokerage fee tables)
- Law: `get_legal_limits` (IKE/IKZE, PIT, minimum wage, voluntary NFZ health insurance contribution, tax on winnings, PCC on private loans and sales of movables, inheritance renunciation and disclaimer costs, local tax limits), `get_next_year_limits` (next-year statutory values from legal formulas before the act, `finwire_next_year_limits`), `get_property_tax_next_year` (next-year property tax limits per m², `finwire_property_tax_cap`), `get_local_fees` (local fees, tourist fee for a stay, transport vehicle tax, agricultural and forest tax, `finwire_resort_fee_family_week`), `get_legal_changes` (acts, bills in the Sejm and the next 30 days of `finwire_legal_calendar`), `get_bills_awaiting_president` (acts awaiting the President's signature with the constitutional deadline and days left, recent signatures, vetoes and referrals to the Constitutional Tribunal), `get_tax_rulings` (tax rulings by personal finance topic), `check_knf_warnings` (a firm or website on the KNF public warnings list), `check_bfg_guarantee` (a bank or credit union on the BFG deposit guarantee list)
- Business in Poland (also for founders from abroad): `plan_business_entry` (forms available to the owner with reasons, taxes and net per form with the treaty dividend rate, setup steps, residence, deadlines), `get_business_forms`, `calc_net_income` (employment, mandate, work contract, B2B), `compare_business_forms` (JDG vs sp. z o.o.), `calc_employer_cost`, `compare_mandate_vs_employment` (mandate vs employment contract: net, cost, paid leave, sick pay, notice, length of employment), `calc_zus_contributions`, `get_tax_treaty` (withholding tax, treaty rates), `get_compliance_calendar`, `get_business_guide` (verified facts with legal basis), `get_business_glossary`
- Business in Poland (also for founders from abroad): `plan_business_entry` (forms available to the owner with reasons, taxes and net per form with the treaty dividend rate, setup steps, residence, deadlines), `get_business_forms`, `calc_net_income` (employment, mandate, work contract, B2B), `compare_business_forms` (JDG vs sp. z o.o.), `calc_employer_cost`, `calc_zus_contributions`, `calc_notice_period` (end of an employment contract after notice or by agreement: notice period, termination date, last payday, holiday equivalent, job search days, severance pay), `calc_business_suspension` (suspending a sole proprietorship: earliest and latest end, ZUS for the part month, health contribution, PIT and VAT deadlines, VAT register), `plan_business_start` (starting a sole proprietorship: steps with dates, ZUS stages and monthly contributions for the first 3 years, VAT and cash register limits), `calc_seniority_bonus` (seniority bonus in the public sector or under a company rule: percent, PLN, next rise), `get_pay_transparency` (pay transparency obligations with dates by headcount, Directive 2023/970 status), `get_tax_treaty` (withholding tax, treaty rates), `get_compliance_calendar`, `get_business_guide` (verified facts with legal basis), `get_business_glossary`
- Regions and Europe: `get_regional_data` (income and PIT per resident by municipality, unemployment, wages), `get_europe_data` (house prices, mortgage rates, bond yields, household electricity and gas prices, minimum wages in the EU), `get_eu_gdp` (GDP per capita in PPS and EUR, AIC, price levels, quarterly GDP growth of EU countries, `finwire_eu_convergence_years`)
- Any dataset: `search_datasets`, `get_dataset`
- Consumer bankruptcies: `get_consumer_bankruptcies` (people declared bankrupt as consumers per month from COIG, year on year, since January, rank in the series, yearly totals, `finwire_consumer_bankruptcies`)
- PIT settlement: `get_pit_settlement_stats` (PIT settlement in Poland by tax year from the Ministry of Finance: taxpayers in each bracket and their share in tax, returns by form, reliefs, child relief, overpayments, flat and lump-sum tax, `finwire_pit_top_bracket_multiplier`)
- Parking spaces: `get_parking_prices` (asking prices of parking spaces, storage rooms and bike boxes from developer price lists per city, type of space, square metres of a new flat a space costs, weekly trend, `finwire_dev_parking_price`)
- Pensioners: `get_pension_earning_limits` (how much a pensioner can earn: 70% and 130% thresholds with dates, finwire estimate before the ZUS announcement, maximum reductions, check of one month)
- Family housing loan: `get_rkm_price_limits` (BGK price limit per m2 for a mortgage without down payment in 52 locations, primary and secondary market, check of a price per m2, share of developer offers within the limit `finwire_rkm_offer_fit`)
- House building cost: `get_build_cost` (replacement cost per m2 announced by voivodes in 52 locations, a 120 m2 house per voivodeship and its change in a year, estimate for a floor area, GUS construction prices, `finwire_house_build_cost`)
- Cumulative inflation: `get_cumulative_inflation` (how much prices in Poland rose between two months or years since 1982 from chained GUS indices, average per year, since January of each recent year, December on December)
- Parental leave: `plan_parental_leave` (dates of maternity, parental and paternity leave of both parents from the birth date, split of weeks, maternity benefit per period, per month and in total from gross pay, 81.5% on request vs 100% and 70%, deadlines, non-transferable weeks a parent loses)
- Cash loan prepayment: `calc_cash_loan_prepayment` (interest with a shorter term vs a lower instalment, early repayment fee under the Consumer Credit Act art. 50: fixed rate only, fee-free threshold of 3 times the December enterprise-sector wage within 12 months, 1% or 0.5% cap, never above interest to the end; refund of up-front costs under art. 49; net saving)
- Salary earned abroad: `calc_foreign_income_pit` (Polish income tax of a Polish resident working in 14 countries: treaty method after the MLI, NBP rate before each payment, 30% per diem exemption, abolition relief cap, surcharge or refund, PIT-36 with PIT/ZG)
- Child benefits: `get_family_benefits` (800 plus and Aktywny Rodzic paid by ZUS per month, amounts, applications, voivodeships with the share of Aktywny Rodzic variants, municipalities with 800 plus per 1000 residents, `finwire_child_benefits_payout`; family allowance with supplements and income thresholds, nursing benefit, Dobry Start, `finwire_family_allowance_real`; social assistance from MOPS: income criteria, permanent and periodic allowance, housing allowance, energy allowance, heating vouchers)
- Savings bond sales: `get_savings_bond_sales` (retail savings bonds sold per month from the Ministry of Finance: series, exchange of maturing bonds, IKE/IKZE, channels, buyer age, since January vs every year, `finwire_savings_bond_sales`)
- Household savings: `get_household_savings` (financial assets of households in Poland per quarter by instrument from NBP financial accounts via the ECB, share outside cash and deposits `finwire_household_invested_share`, Poland among OECD countries)

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
| `GET /v1/series/fx/{code}?days=365` | FX history, e.g. `EUR`, `USD`, `CHF`, `GBP`; table B currencies weekly, e.g. `RUB`, `AED` |
| `GET /v1/series/fx-ranking?limit=20` | Most expensive currencies against PLN, NBP tables A and B, place 52 weeks earlier. `limit`: 1-200 |
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
| `GET /v1/housing/municipalities?voivodeship=&q=&limit=&offset=&sort=&order=` | Every municipality (gmina) with deeds, in pages (default 100, max 500, `next` = next page): last 4 full quarters of the county, deeds, median and quartiles per m² from 30 deeds, primary vs secondary market, YoY, deeds per 1000 residents |
| `GET /v1/housing/municipalities/{teryt}` | One municipality by 7-digit TERYT code: the window and quarterly medians since 2010 |
| `GET /v1/housing/location-premium/{city}` | Districts of a city: actual vs expected price per m² from neighbourhood quality, premium %, model fit (`finwire_location_premium`) |
| `GET /v1/local-tax-squeeze` | Property tax as % of property tax + PIT share in municipal budgets: Poland, provinces, cities, municipalities in the squeeze (`finwire_local_tax_squeeze`); `/municipalities` for every municipality |
| `GET /v1/jst-budget/municipalities` | Municipal budgets: spending, debt and EU funding per resident of every municipality (`finwire_gmina_spending_per_capita`); `/municipalities/{teryt}` for one municipality by category vs neighbours, similar municipalities and Poland; `/ranking` by metric |
| `GET /v1/series/housing` and `/{city}` | GUS housing prices per m² for 17 cities, latest quarters, nominal and real YoY |
| `GET /v1/rent` and `/{city}` | Asking rents: median, mean, per m², month-over-month change, districts |
| `GET /v1/rent/yield` | Gross and net rental yield and payback period per city |
| `GET /v1/housing/carry` | Housing carry per city: net rental yield minus mortgage rate, monthly (`finwire_housing_carry`) |
| `GET /v1/cost-of-living` | Monthly cost of housing and living of a family of 2+1 in 17 cities, renting or owning, with each component and its period (`finwire_city_cost_of_living`) |
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
| `GET /v1/local-fees` | Upper limits of local fees (tourist and spa fee per day until 2026 and per night from 2027, dog, market, advertising fee) and transport vehicle tax per tax year, `finwire_resort_fee_family_week` (7 nights, 2+2), agricultural and forest tax per hectare |
| `GET /v1/legal-limits/local-tax/next-year` | Next-year upper limits of the local property tax from the formula in art. 20 (`finwire_property_tax_cap`): stage (szacunek, wskaznik_gus, obwieszczenie, obowiazuje), half-year CPI index, 9 categories with 80% interval, estimate path and accuracy |
| `GET /v1/macro/indicators` | Headline indicators with the latest reading, change, sparkline and source |
| `GET /v1/macro/indicators/{key}/history?from=2020-01` | Full history of one indicator |
| `GET /v1/macro/consumer-sentiment` | GUS BWUK, WWUK and components with the history |
| `GET /v1/wages/enterprise` | GUS average wage in the enterprise sector, real change, by province |
| `GET /v1/series/gold` | NBP gold price: latest, period changes, all-time high, 12-month average |
| `GET /v1/series/gold/history?range=1y` | Gold price history, `range`: `1m`, `1y`, `5y`, `max` |
| `GET /v1/series/gold/fineness?range=1y` | Price of 1 g of gold of each fineness legal in Poland (999-333) with karats, troy ounce in USD at the NBP USD mid rate, previous and year-ago quote, all-time high, history; `range`: `1y`, `5y`, `max` |
| `GET /v1/fuel-prices` | Maximum fuel prices: in force today, announced for tomorrow, history |
| `GET /v1/oil-prices?history=1y` | Crude oil Brent and WTI (EIA spot prices) in USD and PLN per barrel, week, month and year change, 52-week range, Brent-WTI spread, `finwire_brent_pln`, crude cost per litre next to Polish pump prices (European Commission Weekly Oil Bulletin); optional history `1y`, `5y` (daily) or `max` (monthly) |
| `GET /v1/fuel/retail-estimate?weeks=4` | finwire estimate of the average petrol 95 and diesel pump price for the next day (`finwire_hurtcast_pb95`, `finwire_hurtcast_diesel`), VAT rate, maximum price while it applies, backtest accuracy (mean absolute error, share of weeks within 10 groszy) and recent backtest weeks against the European Commission Weekly Oil Bulletin. Wholesale prices are not redistributed |
| `GET /v1/currencies/forecast?currency=EUR` | finwire exchange rate forecast against PLN (Finwire FXcast) for 12 currencies, optional `currency` and `horizon` (`1d`, `1w`, `1m`, `3m`, `6m`, `12m`): point, forward rate, 50% and 80% ranges, backtest accuracy and accuracy of resolved forecasts; only horizons that passed the accuracy gate. A model range, not investment advice |
| `GET /v1/statutory-interest` | Statutory, late payment, maximum, tax arrears and commercial interest rates from NBP rates, each with the date from which it applies and the next rate |
| `GET /v1/prices/retail` | Average retail prices of 30 goods and services (GUS), changes, minutes of work, finwire shopping basket; `?product=` for one price history |
| `GET /v1/subsidies/clean-air` | Czyste Powietrze programme (NFOŚiGW): applications, contracts and payouts cumulative, Modernisation Fund edition, last week, payout queue and pace, heat sources, reading history |
| `GET /v1/pensions` | Average ZUS pension gross and net, other pensions, allowances, pension to wage ratio, monthly history |
| `GET /v1/pension-payment-dates` | ZUS pension payment calendar for 12 months (dates moved before weekends and public holidays), next payment for each day, 800+ deadlines, minimum pensions, social pension, minimum pension top-up conditions (art. 87), supplementary benefit for persons unable to live independently (threshold, maximum, rule), recipients |
| `GET /v1/average-pole` | Average Pole (`finwire_average_pole`): traits of the typical person in Poland with measure (median, mean, model, distribution, mode), period and source |
| `GET /v1/daily-price-index` | Daily price index finwire (`finwire_daily_price_index`, experimental): daily series, monthly readings with m/m, basket categories, food part vs GUS CPI food, collection state and basket list without prices |
| `GET /v1/pension-forecast` | Pension forecast finwire (`finwire_pension_forecast`): headline (man, average wage, retiring 30 years after the base year), grid birth year x profile x wage level with replacement rates and pension in today's money (P10/P50/P90), fan by retirement year, decomposition, working-age population, OECD and EC benchmarks, parameters |
| `GET /v1/business-registrations?months=24` | Applications filed in CEIDG (MRiT): registrations, closures, suspensions and resumptions of sole proprietorships, net, same month in earlier years, share of women, `finwire_new_business_ratio`, monthly history |
| `GET /v1/new-companies?months=24` | New commercial companies entered in KRS per month by legal form (Ministry of Justice KRS API), other business-register entities, CEIDG sole proprietorships for the same month, current month so far, `finwire_new_companies_pulse`, monthly history |
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
| `GET /v1/bonds/next-rate?series=EDO1235` | Finwire Bondcast: rate of Polish savings bonds in the next interest period (index = 2nd year of EDO bought 12 months before next month), periods starting in the next 3 months by type, formula inputs (CPI, Finwire Nowcast, NBP reference rate, next RPP decision), how the Ministry of Finance followed RPP decisions; with `series`, all periods of the series |
| `GET /v1/fixed-cost-burden?region=mazowieckie` | Sztywne koszty gospodarstwa finwire: share of household net income taken by fixed costs, Poland and 16 provinces, components in PLN, monthly history |
| `GET /v1/ike-ikze` | IKE and IKZE (KNF, half-yearly): accounts, assets, contributions, withdrawals and returns by institution type, holders by sex and age, average contribution vs annual limit, `finwire_ike_ikze_avg_balance`, history in PLN |
| `GET /v1/ofe?months=24` | OFE (KNF, monthly): members, accounts, contributions from ZUS, accounting unit and net assets by fund and total; net rates of return over 12, 36, 60 and 120 months (KNF, April and October); 100 PLN in OFE vs 100 PLN on the ZUS subaccount over the same window; yearly subaccount indexation; `finwire_ofe_vs_subaccount_10y` |
| `GET /v1/car-insurance` | Car insurance (KNF quarterly bulletin, part D): average OC (class 10) and autocasco (class 3) premium per policy over 4 quarters and in the quarter, policies written and in force, claims paid per 100 PLN of premium, 18 non-life classes, `finwire_oc_avg_premium`, quarterly history since 2017 |
| `GET /v1/life-insurance` | Life insurance (KNF quarterly bulletin, part D, class I): average annual premium per individual contract over 4 quarters for the class I total and groups 1-5 with change vs a year earlier, group premium per insured person, individual and group contracts in force, insured persons, single-premium share, claims and benefits paid per 100 PLN of premium, `finwire_life_avg_premium`, quarterly history since 2017 |
| `GET /v1/investment-funds?months=24` | Investment funds (IZFiA): assets, purchases, redemptions and net flows of the market and fund groups, bond types, equity regions, `finwire_fund_flows_index`, monthly history in PLN million |
| `GET /v1/public-debt` | State Treasury debt monthly since 2001, debt per resident, growth per second, structure, public debt % of GDP, budget deficit with plan |
| `GET /v1/energy/regulated-prices?date=2026-10-29` | Household electricity and gas tariffs approved by URE in force on a day (default today): energy and distribution rates, fees, cheaper-zone hours, decision and PDF per row; net amounts |
| `GET /v1/brokers` | Broker fee tables: commissions by market, account and FX fees, IKE/IKZE, active promotions, fee table URL and weekly check status |
| `GET /v1/legal-changes?days=90` | Acts entering into force soon and recently, open bills in the Sejm |
| `GET /v1/legal-changes/process/{term}/{number}` | One bill: last stage, passage, published act |
| `GET /v1/legal-changes/president-desk?finance=true` | Acts awaiting the President's decision with the deadline and days left; signatures, vetoes and referrals to the Constitutional Tribunal of the last 30 days |
| `GET /v1/legal-calendar?kategorie=przepisy,wartosci&od=2026-11-01&do=2027-03-31` | Legal calendar `finwire_legal_calendar`: dated events with category, legal basis, source and finwire.pl page, the number of changes in acts and values in the next 30 days. `kategorie`: `przepisy`, `wartosci`, `podatki`, `firma`, `oszczednosci`, `obligacje`, `stopy` |
| `GET /v1/knf-warnings` | KNF public warnings list: entries by legal basis, prosecutors, new entries per full week (`finwire_knf_warnings_monitor`), newest entries verbatim from KNF |
| `GET /v1/bfg-guarantee` | BFG deposit guarantee list: counts of banks and credit unions with history (`finwire_bfg_guarantee_monitor`), latest changes, EUR limit and its PLN equivalent; `/search?q=` looks up an entity |
| `GET /v1/bank-penalties?bank=mbank` | UOKiK and KNF fines of Polish banks from the last 5 years: count and sum per bank (`finwire_bank_penalty_count`); with `bank` the fines of one bank with links to the decisions |
| `GET /v1/credit-judgments?category=wibor` | Court rulings in WIBOR disputes: monthly counts, share ruled for the borrower at least in part (`finwire_borrower_win_share`), instance I vs II, newest rulings with case number, court, outcome and Court Rulings Portal link |
| `GET /v1/tax-rulings?topic=najem_prywatny` | Tax rulings on a personal finance topic: weekly counts (`finwire_tax_rulings_pulse`), most cited provisions, newest rulings with signature, authority, shortened thesis and EUREKA link |
| `GET /v1/knf-warnings/search?q=tradereu.com` | Look up a firm by name, website, KRS, NIP or REGON; `onList: false` does not mean the firm holds a KNF licence |
| `GET /v1/income/municipalities?year=2025` | Residents income per capita by municipality: rank, ratio to the national average, change |
| `GET /v1/income/counties?year=2025` | The same by county |
| `GET /v1/housing/upkeep/{city}` | Monthly running costs of a flat in a city (list: `/v1/housing/upkeep`) |
| `GET /v1/housing/europe` | Eurostat house price index: Poland vs EU vs euro area and country ranking |
| `GET /v1/housing/europe/overburden` and `/overcrowding` | Housing cost overburden and overcrowding rates by country |
| `GET /v1/europe/energy-prices?product=electricity&compare=DE` | Household electricity or gas prices per kWh in EU countries (Eurostat, half-yearly): ranking in EUR and PPS, Poland in PLN, EU average, history since 2007, `finwire_eu_power_price_gap`, kWh per monthly minimum wage |
| `GET /v1/europe/fuel-prices?fuel=diesel&compare=HR` | Fuel prices at filling stations in EU countries (European Commission Weekly Oil Bulletin, weekly since 2005): ranking in EUR and PLN per litre with and without taxes, EU and euro area averages, last year weekly, `finwire_eu_fuel_price_gap` |
| `GET /v1/europe/gdp-per-capita?compare=DE` | GDP per capita in EU countries (Eurostat): ranking in PPS (EU27 = 100), EUR per inhabitant, real growth per capita, AIC consumption and price level, latest quarter of GDP growth, history since 1995, `finwire_eu_convergence_years` (years to the EU average at the 10-year pace) |
| `GET /v1/europe/minimum-wages?compare=DE` | Monthly statutory minimum wages in EU countries (Eurostat, half-yearly): ranking in EUR and PPS, medians, history of Poland and a comparison country |
| `GET /v1/europe/inflation?compare=DE&since=2015&country=HU` | HICP inflation in EU countries (Eurostat, monthly since 2000): ranking with 5 components, Poland, EU and euro area, history, price growth since a chosen year, `finwire_eu_inflation_gap`; with `country` its component history |
| `GET /v1/europe/unemployment?compare=DE` | Unemployment rate in EU countries (Eurostat une_rt_m, monthly, seasonally adjusted, ILO definition): total and under 25, rank, Poland rank each month since 2010, history of Poland, EU, euro area and a comparison country, `finwire_eu_youth_unemployment_ratio` |
| `GET /v1/europe/net-earnings?household=single&compare=DE&salary=7000` | Annual net earnings in EU countries by household type (Eurostat, yearly): ranking in EUR and PPS, gross, tax, contributions, labour cost per hour, tax wedge, `finwire_eu_net_wage_gap`; `salary` (monthly net PLN) compared with the average in every country |
| `GET /v1/europe/public-debt?compare=DE` | Government debt, deficit and interest in EU countries (Eurostat), % of GDP: latest quarter and year with ranks, countries above the 3% and 60% reference values, history of Poland, the EU and a comparison country, `finwire_eu_debt_threshold_gap` |
| `GET /v1/europe/living-conditions?compare=DE` | Living conditions in EU countries (Eurostat EU-SILC, yearly since 2005): share of people in their own home without and with a mortgage, in market and reduced rent, people who cannot face an unexpected expense, median and mean equivalised income in EUR and PPS, ranks, history of Poland, the EU and a comparison country, `finwire_eu_mortgaged_owners`, `finwire_eu_household_income_gap` |
| `GET /v1/household-inflation?months=24` | Retiree inflation (Statistics Poland CPI for retirees and pensioners vs total, year-to-date average, which index is higher for pension indexation under art. 89(2)), basket shares, household inflation for family types and income quintiles (`finwire_household_inflation_index`) |
| `GET /v1/household-savings?quarters=20` | Household savings in Poland: financial assets by instrument per quarter (NBP via ECB QSA), share outside cash and deposits (`finwire_household_invested_share`), Poland among OECD countries, funds, PPK, IKE/IKZE, retail bonds |
| `GET /v1/forecasts/poland` | Forecasts for Poland: latest and previous edition of the European Commission (AMECO), OECD Economic Outlook, NBP projection and Finwire Nowcast; outturns, forecast errors and `finwire_forecast_accuracy`; Finwire Konsensus only after the accuracy gate |
| `GET /v1/oecd/comparison?topic=tax-wedge&profile=single_100` | Poland vs OECD countries (OECD, yearly): pension replacement rates net and gross for men and women with the rank of Poland, tax wedge for 6 household profiles in every OECD country with an EU flag, history of Poland and the OECD average since 2000, `finwire_oecd_pension_gap`, `finwire_oecd_tax_wedge_gap` |
| `GET /v1/tourism` | Tourism (GUS BDL): `finwire_tourism_pressure` (bed places on 31 July per 1000 residents, Poland and top municipalities), nights and tourists per year for Poland, provinces and top counties, nights and bed occupancy per month |
| `GET /v1/tourism/municipalities?q=Zakopane` | Municipalities matching a name: bed places, residents, `finwire_tourism_pressure`, rank, county nights per resident |
| `GET /v1/pension-systems` | Average ZUS pension vs average KRUS farmer pension (GUS BDL): yearly since 1999, latest year-to-date period with the previous year, 16 provinces |
| `GET /v1/credit-market/survey?quarters=12` | NBP bank lending survey |
| `GET /v1/credit-market/monetary?months=25` | NBP household loans, deposits and new loans |
| `GET /v1/credit-market/banking-sector?months=36` | KNF banking sector: housing and consumer loans with impaired share, household deposits, profit and loss year-to-date, in the month and over 12 months, `finwire_bank_interest_share` |
| `GET /v1/credit-market/demand?months=12` | BIK housing loan demand, requested vs granted amount, granted loans (BIK) and new housing loans (NBP) |
| `GET /v1/credit-market/rate-risk?days=30` | Ryzyko raty finwire (chance the payment rises by more than 5% or 10% in 3, 6 and 12 months, payment percentiles, backtest since 2008) and Premia za stałą stopę finwire (5-year fixed minus expected variable cost, daily and monthly from ECB MIR, negative premium windows) |
| `GET /v1/credit-market/ecb-rates?product=mortgage_total&period=2026-08` | ECB MIR rate on new loans by country |
| `GET /v1/credit-market/ecb-rates/series?areas=PL,U2,DE&from=2018-01` | ECB MIR history for selected countries |
| `GET /v1/calendar?limit=10` | Upcoming releases and decisions with date, Warsaw time, category, importance 1-10 |
| `GET /v1/market/deposit-rates?amount=10000&period=3` | Term deposit rates: median, percentiles, spread |
| `GET /v1/market/savings-rates?amount=10000` | Savings account rates: median, percentiles |
| `GET /v1/market/account-costs?group=young` | Personal account cost by group (all, young people and children, foreign currency accounts from KNF): median, percentiles |
| `GET /v1/market/mortgage-rates?amount=400000&period=25` | Mortgage APRC (RRSO), margin, nominal rate: median, percentiles |
| `GET /v1/market/cash-loan-rates?amount=20000&period=48` | Cash loan APRC and nominal rate: median, percentiles |
| `GET /v1/consumer-bankruptcies?months=24` | Consumer bankruptcies (COIG, from court announcements in KRZ and MSiG): latest month with year-on-year change, sum since January vs the same period a year earlier, last 12 months, rank of the latest month in the series and among the same calendar month, yearly totals, `finwire_consumer_bankruptcies`, monthly history; data COIG, reuse with attribution |
| `GET /v1/pit/national?years=3` | PIT settlement in Poland by tax year (Ministry of Finance yearly information): scale taxpayers, returns by form, brackets with the upper bracket share in tax due, reliefs deducted from income, child relief, overpayments, flat tax, PIT-38, PIT-39, lump-sum tax returns, `finwire_pit_top_bracket_multiplier` |
| `GET /v1/developer-parking-prices?city=warszawa&weeks=26` | Parking space prices from developer price lists (dane.gov.pl): median, quartiles and counts per city and week, underground vs outdoor vs garage, storage rooms and bike boxes, square metres of a new flat a space costs, change vs 4 weeks earlier, weekly trend, `finwire_dev_parking_price` |
| `GET /v1/rkm-price-limits?location=krakow&quarters=4` | Price limits per m2 in the family housing loan (BGK, from voivode announcements): 52 locations (voivodeship city, neighbouring municipalities, other municipalities), primary market (first buyer from the developer) and secondary market, change vs previous quarter and year, quarterly history, `finwire_rkm_offer_fit`, state of government housing programmes (`housingPrograms`); data BGK, cite with attribution |
| `GET /v1/build-cost?voivodeship=malopolskie&months=24` | House building cost: replacement cost per m2 of a residential building (average of the two latest voivode announcements, derived from BGK limits) for the voivodeship city, neighbouring municipalities and other municipalities, a 120 m2 house per voivodeship with the change in a year, `finwire_house_build_cost` with quarterly history, GUS construction and assembly price indices by division (m/m, y/y). `voivodeship` filters one voivodeship, `quarters` and `months` trim the history. An official indicator, not a contractor quote. |
| `GET /v1/cumulative-inflation?from=2022&to=2026` | Cumulative CPI inflation in Poland: result for `from`-`to` (YYYY or YYYY-MM; a year in `from` starts in January, in `to` ends in December or in the latest published month) with the base month, number of months, price factor and average per year; every year with December-on-December inflation and the GUS yearly average; cumulative inflation from January of each year to the latest month. Chained GUS indices, GUS data CC BY 4.0. |
| `GET /v1/parental-leave-plan?birthDate=2027-03-15&motherGross=8000&fatherGross=9000` | Parental leave plan in Poland (all parameters optional: `birthDate` YYYY-MM-DD, default today; `children` 1-5; `prenatalWeeks` 0-6; `motherParentalWeeks` 0-43, default 32; `fatherParentalWeeks` 0-43, default 9; `paternityWeeks` 0-2, default 2; `motherGross`, `fatherGross` average gross monthly pay of 12 months in PLN, without them dates only; `request815` true or false, default true): periods with dates, rate, daily benefit and amount, benefit per month for each parent, totals and the family total of the other variant, deadlines, unused non-transferable weeks. Labour Code art. 180-183 and the sickness and maternity benefits act art. 29a-31, rules in force now. |
| `GET /v1/labour-office-support?employmentEnd=2026-10-31&sicknessStart=2026-11-10` | Start-up grants and labour office support in Poland (all parameters optional; `employmentEnd` and `sicknessStart` YYYY-MM-DD go together and add the sickness benefit check; `sicknessEnd` YYYY-MM-DD; `longIncubation`, `pregnancyOrTb` true or false): average wage in force and the next one when announced, maximum amounts in PLN and multiples of the average wage for each source, conditions and repayment rules, education loan, sickness benefit after the end of employment (14/90-day window, 30 days, 91 days, exclusions) and the check result. |
| `GET /v1/cash-loan-prepayment?amount=50000&months=60&paidInstalments=12&prepayment=10000` | Cash loan prepayment in Poland (all parameters optional: `amount` PLN, default 50000; `months` 1-180, default 60; `ratePct` %, default the NBP average for new consumer loans; `paidInstalments`, default 12; `prepayment` PLN, default 10000; `fixedRate` true or false, default true; `contractFeePct` % of the amount repaid, default 1; `upfrontCosts` PLN, default 2500; `priorPrepayments12m` PLN, default 0): balance, interest in three scenarios (no prepayment, shorter term, lower instalment) with interest by year, early repayment fee with the rule applied and the fee-free threshold, refund of up-front costs, net saving. Consumer Credit Act art. 48-50, rules in force now. |
| `GET /v1/family-benefits?months=12&gmina=krakow` | Child benefits paid by ZUS: 800 plus and Aktywny Rodzic (working parents, nursery, at home) - benefits paid in the month, PLN in the month and since January, 800 plus applications and refusals, 16 voivodeships with the share of Aktywny Rodzic variants, municipalities with 800 plus per 1000 residents (`gmina` = name or TERYT), `finwire_child_benefits_payout`; ZUS data CC0 1.0. Block `familyBenefitAmounts`: family allowance, supplements, income thresholds, birth grant, care benefit and special care allowance from the Council of Ministers regulation, nursing benefit (from the minimum wage), Dobry Start, `finwire_family_allowance_real`. Block `socialAssistance`: social assistance income criteria and maximum permanent allowance, allowance rules, housing allowance thresholds, energy allowance and heating vouchers with application windows |
| `GET /v1/savings-bond-sales?months=24` | Retail savings bond sales (Ministry of Finance statistics): latest month with month-on-month and year-on-year change, series shares and amounts, exchange and new money, IKE/IKZE, channels, buyer age, sum since January with rank among years, yearly totals, `finwire_savings_bond_sales`, monthly history; gov.pl, reuse with attribution |

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
| `GET /v1/world/policy-rates?history=24` | Central bank policy rates of BIS economies: ranking, real rates (minus CPI), place of the NBP rate `finwire_policy_rate_rank` with monthly history, real effective exchange rate of the zloty |
| `GET /v1/world/house-prices` | BIS residential property prices by country (real and nominal y/y), real index of Poland, OECD price-to-income and price-to-rent ratios |
| `GET /v1/world/credit-cycle` | BIS credit-to-GDP gap of Poland and debt service ratio of the private non-financial sector |
| `GET /v1/world/housecast` | Finwire Housecast `finwire_housing_valuation_gap`: valuation gap, real house price forecast with 80% interval and backtest |
| `GET /v1/car-costs?days=30` | Cost of driving 100 km per day (`finwire_car_cost_100km`) with petrol 95, diesel, LPG, home G12 and public AC and DC charging for the ICCT reference car, daily prices and their sources, NBP EUR and USD rates, customs duty, import VAT, PCC threshold, toll motorways, speeding fines. `days` trims the history |
| `GET /v1/us-economy?months=24` | US economy: CPI y/y and m/m, core CPI and Polish CPI since 2011; nonfarm payrolls change, unemployment, average hourly earnings, `finwire_us_real_wage_growth`; Treasury par yield curve now and a year earlier, 10Y-2Y and 10Y-3M spreads, inverted days in 365, weekly history since 2010; public debt with `finwire_us_debt_per_second` in USD and PLN. `months` trims the histories |

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
| `finwire_bond_next_rate` | [Finwire Bondcast](https://finwire.pl/obligacje-skarbowe/oprocentowanie-w-kolejnym-okresie) | savings | monthly | - |
| `finwire_fixed_cost_burden` | [Sztywne koszty gospodarstwa finwire](https://finwire.pl/indeksy/sztywne-koszty-gospodarstwa) | housing | monthly | - |
| `finwire_heating_season_index` | [Indeks chłodu sezonu finwire](https://finwire.pl/sezon-grzewczy) | housing | daily | - |
| `finwire_heating_season_cost` | [Koszt sezonu grzewczego finwire](https://finwire.pl/sezon-grzewczy#koszt-sezonu) | housing | daily | - |
| `finwire_ike_ikze_avg_balance` | [Średnie saldo IKE i IKZE finwire](https://finwire.pl/kalkulatory/ike-ikze) | savings | half-yearly | - |
| `finwire_ofe_vs_subaccount_10y` | [Różnica OFE i subkonta ZUS finwire](https://finwire.pl/ofe) | savings | half-yearly | `ofe` |
| `finwire_oc_avg_premium` | [Średnia składka OC finwire](https://finwire.pl/ubezpieczenia/srednia-cena-oc) | market | quarterly | - |
| `finwire_life_avg_premium` | [Średnia składka na polisę na życie finwire](https://finwire.pl/ubezpieczenie-na-zycie) | market | quarterly | - |
| `finwire_bank_interest_share` | [Udział odsetek w przychodach banków finwire](https://finwire.pl/bank#ile-zarabiaja-banki) | market | monthly | - |
| `finwire_eu_inflation_gap` | [Luka inflacji Polska-UE finwire](https://finwire.pl/inflacja-w-europie) | market | monthly | - |
| `finwire_clean_air_payout_rate` | [Wskaźnik wypłat Czystego Powietrza finwire](https://finwire.pl/czyste-powietrze) | housing | weekly | - |
| `finwire_public_debt_per_capita` | [Dług na osobę finwire](https://finwire.pl/dlug-publiczny) | market | monthly | - |
| `finwire_poland_rating` | [Rating Polski finwire](https://finwire.pl/dlug-publiczny#rating) | market | yearly (end of year, current year as of today) | - |
| `finwire_pit_per_capita` | [PIT na mieszkańca finwire](https://finwire.pl/najbogatsze-gminy#podatek-pit) | market | yearly | - |
| `finwire_pit_top_bracket_multiplier` | [Mnożnik drugiego progu PIT finwire](https://finwire.pl/rozliczenie-pit#pit-w-liczbach) | law | yearly | `pit-national` |
| `finwire_rcn_price_index` | [Indeks cen transakcyjnych finwire](https://finwire.pl/raporty/indeks-cen-transakcyjnych) | housing | quarterly | - |
| `finwire_price_gap_index` | [Indeks luki cenowej finwire](https://finwire.pl/indeksy/luka-cenowa) | housing | quarterly | - |
| `finwire_entry_threshold_index` ¹ | [Próg wejścia finwire](https://finwire.pl/prog-wejscia) | housing | daily | `entry-threshold` |
| `finwire_housing_affordability_barometer` ¹ | [Barometr dostępności mieszkania finwire](https://finwire.pl/barometr-dostepnosci-mieszkania) | housing | monthly | `housing-affordability` |
| `finwire_housing_carry` ¹ | [Carry nieruchomości finwire](https://finwire.pl/indeksy/carry-nieruchomosci) | housing | monthly | `housing-carry` |
| `finwire_city_cost_of_living` | [Koszt życia w mieście finwire](https://finwire.pl/koszty-zycia) | housing | monthly | `cost-of-living` |
| `finwire_housing_permits_index` | [Indeks pozwoleń na budowę finwire](https://finwire.pl/pozwolenia-na-budowe) | housing | monthly | - |
| `finwire_housing_pressure` | [Presja demograficzna na mieszkania finwire](https://finwire.pl/indeksy/presja-demograficzna) | housing | yearly | - |
| `finwire_housing_pipeline` | [Podaż mieszkań za 2 lata finwire](https://finwire.pl/indeksy/podaz-mieszkan) | housing | quarterly | - |
| `finwire_rent_forecast` | [Prognoza czynszów finwire](https://finwire.pl/indeksy/podaz-mieszkan#czynsze) | housing | monthly | - |
| `finwire_eu_mortgaged_owners` | [Właściciele mieszkań z kredytem finwire](https://finwire.pl/ceny-mieszkan/europa) | housing | yearly | `eu-living-conditions` |
| `finwire_offer_ticker` | [Ticker zmian ofert finwire](https://finwire.pl/zmiany-ofert) | market | daily | `offer-changes` |
| `finwire_demand_index` | [Indeks popytu finwire](https://finwire.pl/indeksy/popyt) | market | monthly | - |
| `finwire_power_price_index` | [Indeks ceny prądu finwire](https://finwire.pl/ceny-pradu-jutro) | market | daily | - |
| `finwire_shopping_basket_index` | [Koszyk zakupowy finwire](https://finwire.pl/ceny-w-sklepach) | market | monthly | - |
| `finwire_cigarette_excise_share` | [Akcyza w cenie papierosów finwire](https://finwire.pl/ceny-w-sklepach#akcyza) | market | yearly | `retail-prices` |
| `finwire_eu_power_price_gap` | [Luka cen prądu Polska-UE finwire](https://finwire.pl/ceny-pradu-w-europie) | market | half-yearly | - |
| `finwire_eu_fuel_price_gap` | [Luka cen paliw Polska-UE finwire](https://finwire.pl/ceny-paliw-w-europie) | market | weekly | `eu-fuel-prices` |
| `finwire_brent_pln` | [Ropa Brent w złotych finwire](https://finwire.pl/ceny-paliw#ropa) | market | daily | `oil-prices` |
| `finwire_hurtcast_pb95` | [Finwire Hurtcast: benzyna 95](https://finwire.pl/hurtowe-ceny-paliw#metoda) | market | daily | `fuel-retail-estimate` |
| `finwire_hurtcast_diesel` | [Finwire Hurtcast: olej napędowy](https://finwire.pl/hurtowe-ceny-paliw#metoda) | market | daily | `fuel-retail-estimate` |
| `finwire_fxcast_eur_3m` | [Finwire FXcast: zasięg kursu euro w 3 miesiące](https://finwire.pl/prognoza-kursu-euro#metoda) | market | daily | `currency-forecast` |
| `finwire_fxcast_usd_3m` | [Finwire FXcast: zasięg kursu dolara w 3 miesiące](https://finwire.pl/prognoza-kursu-dolara#metoda) | market | daily | `currency-forecast` |
| `finwire_fxcast_gbp_3m` | [Finwire FXcast: zasięg kursu funta w 3 miesiące](https://finwire.pl/prognoza-kursu-funta#metoda) | market | daily | `currency-forecast` |
| `finwire_fxcast_chf_3m` | [Finwire FXcast: zasięg kursu franka w 3 miesiące](https://finwire.pl/prognoza-kursu-franka#metoda) | market | daily | `currency-forecast` |
| `finwire_fxcast_nok_3m` | [Finwire FXcast: zasięg kursu korony norweskiej w 3 miesiące](https://finwire.pl/prognoza-kursu-korony-norweskiej#metoda) | market | daily | `currency-forecast` |
| `finwire_eu_youth_unemployment_ratio` | [Mnożnik bezrobocia młodych finwire](https://finwire.pl/bezrobocie-w-europie) | market | monthly | - |
| `finwire_eu_net_wage_gap` | [Pensja w Polsce wobec średniej UE finwire](https://finwire.pl/zarobki/europa) | market | yearly | - |
| `finwire_eu_convergence_years` | [Licznik doganiania UE finwire](https://finwire.pl/pkb-per-capita) | market | yearly | `eu-gdp-per-capita` |
| `finwire_eu_debt_threshold_gap` | [Dług Polski wobec progu 60% PKB finwire](https://finwire.pl/dlug-publiczny/europa) | market | quarterly | - |
| `finwire_eu_household_income_gap` | [Dochód gospodarstw w Polsce wobec UE finwire](https://finwire.pl/zarobki/europa) | market | yearly | `eu-living-conditions` |
| `finwire_household_invested_share` | [Udział oszczędności pracujących finwire](https://finwire.pl/oszczednosci-polakow) | savings | quarterly | `household-savings` |
| `finwire_forecast_accuracy` | [Trafność prognoz finwire](https://finwire.pl/prognozy-dla-polski#trafnosc) | market | yearly | `poland-forecasts` |
| `finwire_nowcast` | [Finwire Nowcast](https://finwire.pl/nowcast) | market | monthly | `nowcast` |
| `finwire_cost_pressure` | [Presja kosztowa finwire](https://finwire.pl/nowcast#presja-kosztowa) | market | monthly (forecast) | - |
| `finwire_legal_calendar` | [Kalendarz zmian w finansach finwire](https://finwire.pl/kalendarz-zmian) | law | daily | - |
| `finwire_knf_warnings_monitor` | [Monitor ostrzeżeń KNF finwire](https://finwire.pl/ostrzezenia-knf) | law | weekly | `knf-warnings` |
| `finwire_bfg_guarantee_monitor` | [Licznik instytucji z gwarancją BFG finwire](https://finwire.pl/rankingi/lokaty#gwarancja-bfg) | savings | on every list change | `bfg-guarantee` |
| `finwire_bank_penalty_count` | [Licznik kar finwire](https://finwire.pl/indeksy?indeks=finwire_bank_penalty_count) | savings | monthly, as of the read day | `bank-penalties` |
| `finwire_tax_rulings_pulse` | [Puls interpretacji podatkowych finwire](https://finwire.pl/rozliczenie-pit) | law | weekly | `tax-rulings` |
| `finwire_borrower_win_share` | [Wyroki dla kredytobiorców finwire](https://finwire.pl/wyroki-kredytowe) | law | monthly | `credit-judgments` |
| `finwire_next_year_limits` | [Limity na przyszły rok finwire](https://finwire.pl/indeksy/limity-na-przyszly-rok) | law | daily | `next-year-limits` |
| `finwire_local_tax_squeeze` | [Nożyce fiskalne gmin finwire](https://finwire.pl/najbogatsze-gminy#nozyce-fiskalne) | housing | yearly | `local-tax-squeeze` |
| `finwire_property_tax_cap` | [Granica podatku od mieszkań finwire](https://finwire.pl/kalkulatory/podatek-od-nieruchomosci#przyszly-rok) | housing | daily | `local-tax-next-year` |
| `finwire_resort_fee_family_week` | [Tydzień rodziny w opłacie miejscowej finwire](https://finwire.pl/oplaty-lokalne#oplata-miejscowa) | law | yearly | `local-fees` |
| `finwire_gmina_spending_per_capita` | [Wydatki gmin na mieszkańca finwire](https://finwire.pl/budzet-gminy) | market | yearly | `jst-budget` |
| `finwire_average_pole` | [Przeciętny Polak finwire](https://finwire.pl/przecietny-polak) | credit | monthly | `average-pole` |
| `finwire_daily_price_index` | [Dzienny indeks cen finwire](https://finwire.pl/dzienny-indeks-cen) | market | daily | `daily-price-index` |
| `finwire_pension_forecast` | [Prognoza emerytur finwire](https://finwire.pl/prognoza-emerytury) | savings | yearly | `pension-forecast` |
| `finwire_location_premium` | [Premia za okolicę finwire](https://finwire.pl/ceny-mieszkan/warszawa#premia-za-okolice) | housing | quarterly | `location-premium` |
| `finwire_coal_price_pl` | [Cena węgla finwire](https://finwire.pl/cena-wegla) | housing | yearly | `coal-waste-prices` |
| `finwire_waste_fee_pl` | [Opłata za odpady finwire](https://finwire.pl/kalkulatory/oplata-za-smieci) | housing | yearly | `coal-waste-prices` |
| `finwire_consumer_bankruptcies` | [Licznik upadłości konsumenckich finwire](https://finwire.pl/upadlosc-konsumencka) | credit | monthly | - |
| `finwire_dev_parking_price` | [Indeks cen miejsc postojowych finwire](https://finwire.pl/ceny-miejsc-postojowych) | housing | weekly | `developer-parking-prices` |
| `finwire_rkm_offer_fit` | [Mieszkania w limicie kredytu bez wkładu finwire](https://finwire.pl/kredyt-bez-wkladu-wlasnego#limity-cen-m2) | housing | daily | `rkm-price-limits` |
| `finwire_house_build_cost` | [Koszt budowy domu finwire](https://finwire.pl/koszt-budowy-domu#dom-120-m2) | housing | quarterly | `build-cost` |
| `finwire_child_benefits_payout` | [Wypłaty na dzieci z ZUS finwire](https://finwire.pl/800-plus) | savings | monthly | `family-benefits` |
| `finwire_family_allowance_real` | [Realny zasiłek rodzinny finwire](https://finwire.pl/swiadczenia-rodzinne) | savings | monthly | `family-benefits` |
| `finwire_savings_bond_sales` | [Licznik obligacji oszczędnościowych finwire](https://finwire.pl/obligacje-skarbowe) | savings | monthly | - |
| `finwire_deposit_choice_premium` | [Premia za wybór lokaty finwire](https://finwire.pl/indeksy/lokaty#premia-za-wybor-lokaty) | savings | monthly | - |
| `finwire_deposit_rate_forecast` | [Finwire Depocast](https://finwire.pl/indeksy/prognoza-oprocentowania-lokat) | savings | monthly | - |
| `finwire_real_avg_deposit_rate` | [Realne oprocentowanie przeciętnej lokaty finwire](https://finwire.pl/indeksy/lokaty#srednie-oprocentowanie-lokat) | savings | yearly | `deposit-rate-history` |
| `finwire_policy_rate_rank` | [Pozycja stopy NBP finwire](https://finwire.pl/stopy-procentowe) | market | monthly | `world-policy-rates` |
| `finwire_housing_valuation_gap` | [Finwire Housecast](https://finwire.pl/prognoza-cen-mieszkan) | housing | quarterly | `housecast` |
| `finwire_oecd_pension_gap` | [Emerytura w Polsce wobec OECD finwire](https://finwire.pl/prognoza-emerytury) | savings | yearly | `oecd-comparison` |
| `finwire_oecd_tax_wedge_gap` | [Klin podatkowy w Polsce wobec OECD finwire](https://finwire.pl/zarobki/europa) | market | yearly | `oecd-comparison` |
| `finwire_new_companies_pulse` | [Puls nowych firm finwire](https://finwire.pl/finanse-firmy) | market | monthly | `new-companies` |
| `finwire_tourism_pressure` | [Presja turystyczna finwire](https://finwire.pl/najem-krotkoterminowy) | housing | yearly | `tourism` |
| `finwire_car_cost_100km` | [Koszt 100 km finwire](https://finwire.pl/koszt-auta#metoda) | market | daily | `car-costs` |
| `finwire_us_debt_per_second` | [Licznik długu USA finwire](https://finwire.pl/gospodarka-usa#licznik-dlugu-usa) | market | daily | `us-economy` |
| `finwire_us_real_wage_growth` | [Realne płace w USA finwire](https://finwire.pl/gospodarka-usa#realne-place-w-usa) | market | monthly | `us-economy` |

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

## License

Repository files (OpenAPI specification, TypeScript types, `server.json`, docs): [MIT](LICENSE). Data from the API: CC BY 4.0 for finwire indicators and aggregates, the original publisher's terms for public data; `meta.license` in each response.

Built by [finwire.pl](https://finwire.pl) - Polish personal finance tools: [mortgage decision tool](https://finwire.pl/decyzjomat/hipoteka), [flat prices by city](https://finwire.pl/ceny-mieszkan), [deposit rankings](https://finwire.pl/rankingi/lokaty).

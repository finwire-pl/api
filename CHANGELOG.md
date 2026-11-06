# Changelog

Changes to the public finwire API (`https://public-api.finwire.pl`) visible to API consumers.
All endpoints are under `/v1`. Breaking changes are marked **Breaking**.

## 2026-11-06

### Added

- `GET /v1/jst-budget/municipalities`, `/municipalities/{teryt}` and `/ranking` (`metric`, `family`, `period`) - dataset `jst-budget`: budgets of every municipality and city with county rights - executed spending per resident by category (Ministry of Finance Rb-28S) vs neighbours, similar municipalities and Poland, investment share, plan execution, debt per resident at quarter end (Rb-Z), EU funding per resident 2021-2027 and 2014-2020 with largest projects (projects located in one municipality; natural persons not named). Index `finwire_gmina_spending_per_capita`.
- `GET /v1/average-pole` - dataset `average-pole`: Average Pole (`finwire_average_pole`), a profile of the typical person in Poland from the latest readings - median age (Eurostat `demo_pjanind`), median gross wage with its net amount (GUS), median price per m2 of flats (GUS), average new mortgage (BIK), model monthly payment (annuity at the average rate on new housing loans), average ZUS pension, spending per person (GUS household budgets), passenger cars by age class (Eurostat `road_eqs_carage`) and the most registered car brand over 12 months. Each trait has `measure` (`median`, `mean`, `model`, `distribution`, `mode`), `period` and `source`.
- MCP server 1.14.0: new tools `get_municipal_budget` (one municipality by name or TERYT, national figures or a ranking) and `get_average_pole`; `jst-budget` and `average-pole` in `search_datasets` and `get_dataset`.
- `GET /v1/ev-charging/networks` and `/networks/{slug}`: field `pointsWithPowerFromMaps` (`openStreetMap`, `openChargeMap`) - count of points whose power class finwire.pl completes from OpenStreetMap (ODbL) or Open Charge Map (CC BY 4.0) because the operator did not report power to EIPA (mostly Tesla Superchargers). Aggregate only; `pointsByPowerClass`, `hpcPoints` and other aggregates include these points and `attribution` then names both sources. New power class key `dc_unknown` (direct current, power not reported). `GET /v1/ev-charging/chargers` and `/chargers/{id}` keep power and class from EIPA only.
- `GET /v1/ev-charging/growth` (optional `network` or `city`) - dataset `ev-charging-growth`: growth of public EV charging - EIPA month ends since December 2019 (locations, stations, points, CNG/LNG) with monthly change, year on year and records; daily snapshots since the start of measurement of sites and points by power class, province and network, or for one network or city. Index `finwire_ev_infrastructure_growth` (charging points year on year, monthly).
- MCP server 1.13.0: `get_ev_charging` adds growth year on year (`finwire_ev_infrastructure_growth`), growth records and daily growth by power class; `ev-charging-growth` in `search_datasets` and `get_dataset`.
- `GET /v1/ev-charging`: new field `powerClasses` - charging sites on the EIPA map by maximum power class (EU AFIR, Regulation (EU) 2023/1804: up to 22 kW, 23-49, 50-149, 150-349, 350+ kW) and connector family (CCS, Type 2, CHAdeMO), DC, fast (>= 50 kW) and ultra-fast (>= 150 kW) sites, top counties by ultra-fast sites, top networks by fast sites. Aggregates only.
- `GET /v1/electric-vehicles`: field `tesla` - Tesla in first registrations of passenger cars (Ministry of Digital Affairs brand files, new and used imports): monthly counts and share of all passenger cars, brand rank, last 12 months, provinces, years with `newBevMaxSharePct` (Tesla registrations / brand-new BEV from Eurostat, an upper bound of Tesla's share of new electric cars). Months without a consistent brand file are `null` and listed in `gapPeriods`.
- EV charging from the UDT register EIPA read with an end-user data account (attribution "Źródło: Ewidencja Infrastruktury Paliw Alternatywnych (UDT)"):
  - `GET /v1/ev-charging/chargers` (`city` or `lat`+`lon` with `radiusKm`; `minKw`, `free`, `available`, `network`, `connector`, `limit`) and `GET /v1/ev-charging/chargers/{id}` - dataset `ev-chargers`: sites with AFIR power class, maximum kW, points, points free now (status younger than 2 hours), lowest ad hoc price per kWh; one site with stations, points, connectors, prices and status age. No operator contact details.
  - `GET /v1/ev-charging/prices` - dataset `ev-charging-prices`: ad hoc price per kWh by power class, AC vs DC, networks and cities (medians with point counts), recent price changes. Index `finwire_ev_charging_price_index`.
  - `GET /v1/ev-charging/networks` and `GET /v1/ev-charging/networks/{slug}` - dataset `ev-charging-networks`: networks with stations, points, median prices, reliability, reach and a weighted ranking; one network price list and price changes. Index `finwire_ev_charger_reliability`.
  - `GET /v1/ev-charging/occupancy` - dataset `ev-charging-occupancy`: status now, weekday x hour heatmap, busiest sites and cities. Index `finwire_ev_charger_occupancy` (published after 14 days of readings).
  - `GET /v1/ev-charging/coverage` (optional `counties=all`) - dataset `ev-charging-coverage`: nearest site from 150 kW per county. Index `finwire_ev_fast_coverage`.
  - `GET /v1/ev-charging/gas` - dataset `alt-fuel-stations`: CNG, LNG and hydrogen stations with prices.
- MCP server 1.12.0: tools `find_ev_chargers` and `get_ev_charging_prices`; `get_ev_charging` summarises power classes with median prices, counties with the most ultra-fast sites and the largest networks, with a link to the charging map [finwire.pl/mapa-ladowarek](https://finwire.pl/mapa-ladowarek); `get_electric_vehicles` reports Tesla.

### Changed

- Passenger-car brands (`/v1/vehicle-registrations`): "TESLA MOTORS" is counted as "TESLA".
- `GET /v1/ev-charging/occupancy`: occupancy is measured from the last known status of points whose network sent any status change in the last 24 hours (a point occupied with no change for more than 7 days counts as no reading); new fields `method`, `measuredNow`, `weekly`, `byNetwork`, `index.previous`; `byCity` adds `averagePointsMeasured`. `finwire_ev_charger_occupancy` is now the value of the last full week (Monday to Sunday) instead of a rolling 7 days (method version 2). `now` keeps the 2-hour "free now" rule of the map.

## 2026-11-05

### Added

- `GET /v1/credit-judgments` (optional `category`, `limit`) - rulings of Polish common courts in consumer credit disputes from the Court Rulings Portal of the Ministry of Justice (via SAOS, ICM UW), classified by finwire.pl into CHF mortgages, other currencies, free credit sanction, early repayment refunds, WIBOR and unit-linked policies: counts by publication month, share ruled for the borrower at least in part, instance I vs II, median time from claim to judgment, newest rulings with case number, court, outcome and a link to the full text. Dataset `credit-judgments`. Index `finwire_borrower_win_share`.
- Heating season, `GET /v1/heating-season` (optional `city`, `history`) and `GET /v1/heating-season/seasons` (optional `city`) - dataset `heating-season`: heating degree days since 1 July from IMGW-PIB synoptic stations (Eurostat method, 18/15 C) vs the 20-season norm for Poland and 17 cities, finwire indices `finwire_heating_season_index` (cold index, 100 = norm) and `finwire_heating_season_cost` (season heating cost of a 100 m2 house on gas with an 80% interval, published only when the backtest gate passes), cost scenarios (gas, heat pump, coal, district heating), monthly means vs norm, season totals and records since 1966/67, inland gas consumption (Eurostat). Aggregates only; mandatory IMGW-PIB attribution in `attribution`.
- `GET /v1/vehicle-registrations` (optional `months`, `period`, `province`) - first registrations of vehicles in Poland from the Ministry of Digital Affairs files with CEPiK data (dane.gov.pl datasets 440 and 487, yearly brand datasets): passenger cars and all vehicles monthly since January 2014, 17 vehicle types, provinces per 1000 residents, months held back by the consistency check (`quarantine`), passenger-car brands of the month and of 12 months with the share of brands from China (from January 2023). Dataset `vehicle-registrations`; attribution "Ministerstwo Cyfryzacji". Indices `finwire_car_registrations_pulse`, `finwire_chinese_brands_share`. MCP tool `get_vehicle_registrations`.
- `GET /v1/electric-vehicles` (optional `counties=all`) - BEV and PHEV passenger cars: fleet and new cars by year (Eurostat road_eqs_carpda, road_eqr_carpda), fuel mix of new cars, provinces and counties per 1000 passenger cars (GUS BDL, `countySource` says whether available), correlation of charging sites and EV adoption across counties. Dataset `electric-vehicles`. Index `finwire_ev_share_new_cars`. MCP tool `get_electric_vehicles`.
- `GET /v1/ev-charging` (optional `months`) - public EV charging from the UDT register EIPA: locations, stations and points today and at month ends since December 2019, sites and fast sites (>= 50 kW) by province and county. Dataset `ev-charging`. Index `finwire_ev_charging_coverage`. MCP tool `get_ev_charging`.
- MCP server 1.10.0: tools `get_credit_judgments`, `get_heating_season`, `get_vehicle_registrations`, `get_electric_vehicles`, `get_ev_charging`. New datasets in `search_datasets` and `get_dataset`.

### Changed

- Discovery manifest moved to `GET /.well-known/ard.json` (Agentic Resource Discovery) with `displayName`, `description`, `representativeQueries` and `capabilities` (tool names); `/.well-known/ai-catalog.json` returns the same manifest.
- MCP server 1.9.1: website of the server is now [finwire.pl/mcp](https://finwire.pl/mcp) (English: [finwire.pl/en/mcp](https://finwire.pl/en/mcp)) with how to connect, a coverage table and example questions; tools unchanged.

### Added

- Business in Poland, `GET /v1/business-poland/*` - calculations and verified legal facts for someone starting or running a business in Poland, also from abroad. Dataset `business-poland`; finwire.pl calculations from the PIT, CIT and social security acts (CC BY 4.0), facts with act, article, link to the official text (ELI) and verification date. Informational, not tax or legal advice.
  - `net-income` (`form` uop, zlecenie, dzielo, b2b; `gross`; optional `costs`, `tax`, `lumpSumRate`, `zus`, `ppk`, `student`, `year`) - monthly take-home pay with ZUS, health contribution, PIT and the cost to the payer.
  - `compare-forms` (`revenue`; optional `costs`, `lumpSumRate`, `zus`, `dividendTax`, `year`) - sole proprietorship (tax scale, flat tax, ryczałt) vs sp. z o.o. with one or many shareholders: yearly ZUS, health, PIT, CIT (small taxpayer test at the NBP EUR rate of the first working day of October of the previous year), dividend tax and net to the owner.
  - `zus-schemes` - ZUS of a sole proprietor: start relief, preferential, full.
  - `forms` (`status`) - every business form with available yes/no for the owner status (EU/EEA, Swiss, non-EU with a listed permit, other non-EU, foreign company), reason and legal basis.
  - `treaty` (optional `country`) - withholding tax on dividends, interest and royalties: statutory rate, tax-treaty rate, MLI, EU parent-subsidiary exemption; without `country`: covered countries and the NBP ranking of direct investors.
  - `compliance` (`form`; optional `vat`, `employees`, `year`) - yearly calendar of tax, ZUS, VAT and reporting deadlines computed from statutory rules with the working-day shift.
  - `plan` (`status`, `revenue`; optional `country`, `costs`, `employees`, `vat`, `lumpSumRate`) - forms available to the owner, taxes and net per form with the treaty dividend rate of the owner country, setup steps, residence facts and the calendar.
  - `net-income`, `compare-forms`, `zus-schemes` and `plan` return `basis`: whether the PIT scale comes from a bill that is not law yet, and for each driver (minimum wage, forecast average wage, average enterprise wage) whether it is official, a finwire estimate from `finwire_next_year_limits` with 80% interval and stage, or taken from the previous year until announced.
  - `guide` (optional `topic`, `q`), `topics`, `glossary` - verified facts by topic (selling into Poland and VAT, residence, PESEL, documents, forms, setup, PKD and regulated activities, withholding tax, treaties, tax residence, health and A1, property for foreigners, contracts, labour law, hiring foreigners, deadlines, accounting, incentives, closing) and a Polish-English glossary. All accept `lang` (`en`, `pl`).
- MCP server 1.9.0: tools `plan_business_entry`, `get_business_forms`, `calc_net_income`, `compare_business_forms`, `calc_employer_cost`, `calc_zus_contributions`, `get_tax_treaty`, `get_compliance_calendar`, `get_business_guide`, `get_business_glossary`; prompt `doing_business_in_poland`.

## 2026-11-04

### Added

- `GET /v1/housing/carry` - housing carry by city: net rental yield of a typical two-room flat minus the variable mortgage rate, in percentage points, monthly. Index `finwire_housing_carry`. MCP tool `get_housing_carry`.
- `GET /v1/credit-market/rate-risk` (optional `days`) - chance that a variable-rate mortgage payment rises by more than 5% in 12 months, payment range with 80% interval in 3, 6 and 12 months, backtest since 2008; premium of a 5-year fixed rate over the expected variable rate, daily and monthly (ECB MIR). Indices `finwire_rate_risk_gauge`, `finwire_fixed_rate_premium`. MCP tool `get_rate_risk`.
- `GET /v1/retail-bond-premium` (optional `months`) - retail savings bonds vs best bank deposits and savings accounts after tax, per series, monthly. Index `finwire_retail_bond_premium`. MCP tool `get_retail_bond_premium`.
- `GET /v1/fixed-cost-burden` (optional `region`, `months`) - share of household net income taken by mortgage payment, admin fee, utilities and TV licence, Poland and 16 provinces, monthly. Index `finwire_fixed_cost_burden`. MCP tool `get_fixed_cost_burden`.
- `GET /v1/legal-limits/next-year` (optional `path=false`) - next-year pension indexation, minimum wage, IKE/IKZE limits, ZUS bases and caps computed from the statutory formulas before the act, with 80% interval, stage (estimate, government proposal, announced, in force) and backtest. Index `finwire_next_year_limits`. MCP tool `get_next_year_limits`.
- `GET /v1/housing/balance` (optional `city`) - new residents per new dwelling (index `finwire_housing_pressure`), completions forecast from building permits with 80% interval and accuracy per horizon (index `finwire_housing_pipeline`), rent forecast status (index `finwire_rent_forecast`, published after passing the accuracy threshold). MCP tool `get_housing_balance`.
- `GET /v1/macro/lead-forecasts` (optional `index`: `cost-pressure`, `npl`) - core inflation in 6 and 12 months (index `finwire_cost_pressure`) and impaired housing and consumer loans in 2 and 4 quarters (index `finwire_npl_forecast`), 80% intervals and backtest; a horizon below the accuracy threshold is returned without the forecast. MCP tool `get_lead_forecasts`.
- `GET /v1/local-tax-squeeze` and `GET /v1/local-tax-squeeze/municipalities` (optional `voivodeship`) - reliance of municipal budgets on property tax (planned property tax vs PIT share, Rb-27S), country, provinces, cities with county rights, municipalities in the squeeze, Ministry of Finance upper rate. Index `finwire_local_tax_squeeze`. MCP: `get_regional_data` topic `local-tax`.
- `GET /v1/housing/location-premium` and `GET /v1/housing/location-premium/{city}` - district price from notarial deeds vs price explained by neighbourhood quality, premium per district, fit and leave-one-out check. Index `finwire_location_premium`. MCP: `get_housing_prices` view `location-premium`.
- MCP server 1.8.0: tools `get_housing_carry`, `get_rate_risk`, `get_retail_bond_premium`, `get_fixed_cost_burden`, `get_next_year_limits`, `get_housing_balance`, `get_lead_forecasts`; `get_regional_data` topic `local-tax`; `get_housing_prices` view `location-premium`. New datasets in `search_datasets` and `get_dataset`.
- `GET /v1/pit/summary`, `GET /v1/pit/municipalities` and `GET /v1/pit/municipalities/{teryt}` (optional `year`) - PIT returns by municipality from the yearly Ministry of Finance release on dane.gov.pl (dataset 3497): PIT due, taxpayers and revenue by type of return, PIT due per resident with GUS population, rank of all municipalities, provinces, revenue per PIT-37 taxpayer and PIT-38 returns per 1000 residents, all tax years since 2022. Dataset `pit-returns`; attribution "Ministerstwo Finansów". Index `finwire_pit_per_capita` (national PIT due per resident, yearly).
- MCP: `get_regional_data` accepts `topic: "pit"` (national summary, or one municipality year by year with `name`).
- `GET /v1/bfg-guarantee` and `GET /v1/bfg-guarantee/search?q=` - banks and credit unions covered by the BFG deposit guarantee: counts of commercial banks, cooperative banks, credit unions and entities in bankruptcy for every change of the list since 2019, latest additions, removals, moves to bankruptcy and renames, EUR limit with its PLN equivalent at the latest NBP rate, lookup by name or website. Dataset `bfg-guarantee`; attribution "BFG, lista podmiotów objętych gwarancjami". Index `finwire_bfg_guarantee_monitor`.
- MCP tool `check_bfg_guarantee`.
- `GET /v1/tax-rulings` (optional `topic`, `limit`) - individual tax rulings of KIS, changes of rulings and municipal rulings from the EUREKA database of the Ministry of Finance by personal finance topic: weekly counts, most cited provisions in the last 52 weeks, newest rulings with signature, authority, dates, shortened thesis and a link to EUREKA. Dataset `tax-rulings`; attribution "Ministerstwo Finansów, EUREKA". Index `finwire_tax_rulings_pulse` (rulings on personal finance topics per full week).
- MCP: tool `get_tax_rulings`; dataset `tax-rulings` in `search_datasets` and `get_dataset`.
- `GET /v1/ppk` (optional `months`) - PPK (Employee Capital Plans) from the monthly PFR Portal PPK bulletin: net assets of target date funds, active accounts, participants, employers, participation rate, average management fee and number of institutions, monthly since October 2021. Dataset `ppk`; attribution "PFR Portal PPK". Index `finwire_ppk_avg_account` (net assets per active account, PLN). MCP tool `get_ppk_stats`.
- `GET /v1/business-registrations` (optional `months`) - applications filed in CEIDG (Ministry of Development and Technology, dane.gov.pl dataset 745): registrations, closures, suspensions, resumptions and changes of sole proprietorships, registrations minus closures, same month in earlier years, share of women, monthly since November 2019. Dataset `business-registrations`; attribution "MRiT". Index `finwire_new_business_ratio` (registrations per 100 closures, sum of 12 months). MCP tool `get_business_registrations`.
- `GET /v1/ike-ikze` - IKE and IKZE (individual retirement accounts) market from KNF half-yearly data: accounts, assets, opened accounts, contributions and withdrawals by institution type, women and men, age groups, since June 2020. Dataset `ike-ikze`; attribution "KNF". Index `finwire_ike_ikze_avg_balance` (assets per account, half-yearly). MCP tool `get_ike_ikze_stats`.
- MCP server 1.7.0: tools `get_business_registrations`, `check_bfg_guarantee`, `get_tax_rulings`, `get_ppk_stats`, `get_ike_ikze_stats`; `get_regional_data` topic `pit`. New datasets in `search_datasets` and `get_dataset`.

## 2026-11-03

### Added

- `GET /v1/investment-funds` - Polish investment funds market from IZFiA monthly data: assets, purchases, redemptions and net flows by fund group (equity, bond, mixed, money market, PPK), monthly since 2020. Dataset `investment-funds`; attribution "IZFiA". Index `finwire_fund_flows_index` (12-month net flows as % of assets a year earlier).
- `GET /v1/subsidies/clean-air` - Czyste Powietrze programme statistics from NFOŚiGW: applications, contracts and payouts (count and amount), payouts as % of contracted, heat source shares. Dataset `clean-air`; attribution "NFOŚiGW". Index `finwire_clean_air_payout_rate`.
- `GET /v1/public-debt` - State Treasury debt (monthly, domestic and foreign), public debt and EDP general government debt (quarterly, % of GDP), debt per person and the state budget execution estimate (revenue, expenditure, balance vs plan) from the Ministry of Finance. Dataset `public-debt`; attribution "Ministerstwo Finansów". Index `finwire_public_debt_per_capita`.
- `GET /v1/europe/energy-prices` and `GET /v1/europe/minimum-wages` - household electricity and gas prices in EU countries (EUR, PPS, national currency, with and without taxes, half-yearly) and statutory minimum wages (half-yearly) from Eurostat. Index `finwire_eu_power_price_gap` (price in Poland as % of the EU average in PPS).
- MCP server 1.6.0: tools `get_investment_funds`, `get_subsidy_stats`, `get_public_debt`; EU energy prices and minimum wages in `get_europe_data`. New datasets in `search_datasets` and `get_dataset`.

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

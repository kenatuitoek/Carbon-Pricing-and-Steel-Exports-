# Carbon Pricing and Steel Exports

An R project analysing the effect of carbon prices on HS72 and HS73 exports.

## Data

### 1. Carbon pricing data

**Source**

- World Bank Group – *Carbon Pricing Dashboard* (Download data section) "https://carbonpricingdashboard.worldbank.org/about#download-data".
- Trading Data : (data_08_2025.xlsx)(can be found in zip file)

**Description**
- Used sheet 4, Compliance Price
- Contains information on carbon pricing instruments (ETS and carbon taxes) by Region and year.
- Includes variables such as:
  - Instrument name and type (ETS, carbon tax)
  - Country / Region
  - Year of operation
  - Carbon price level (US$/tCO₂e)
  - Coverage and instrument status

**Processing steps**

- Selected national-level instruments only (no subnational schemes unless explicitly required).
- Converted the raw dataset to a **country–year panel**:
  - Mapped regions to ISO3 country codes using `countrycode` in R.
  - Expanded the EU ETS to all participating EU/EEA countries where needed.
  - Expanded the ETS RIGG(north american states) to USA country.
  - Aggregated multiple instruments within a country–year into:
    - `ETS_Price_Mean`, `Tax_Price_Mean`
    - `All_Price_Mean` (overall mean carbon price, trade-weighted in later steps)
    - Counts of schemes (`Num_ETS`, `Num_Tax`) and a categorical `Carbon_Price_Type`.

The final carbon-pricing panel has one observation per **country–year** with harmonised ISO3 codes.

---

### 2. Trade data

**Source**

- UN Comtrade data accessed via **World Integrated Trade Solution (WITS)**.
- Navigate to "Advanced Query" or "Trade Data"
- Reporter: Select the following 36 countries/regions:
- EU27: Austria, Belgium, Bulgaria, Croatia, Cyprus, Czech Republic, Denmark, Estonia, Finland, France, Germany, Greece, Hungary, Ireland, Italy, Latvia, Lithuania, Luxembourg, Malta, Netherlands, Poland, Portugal, Romania, Slovakia, Slovenia, Spain, Sweden
- Other European: United Kingdom, Norway, Switzerland, Iceland
- Americas: United States, Canada
- Asia-Pacific: Japan, South Korea, China, Australia, New Zealand
- Partner: World (total exports)
  Product:
  HS 2-digit: Chapter 72 (Iron and steel)
  HS 2-digit: Chapter 73 (Articles of iron or steel)
- Trade Flow: Exports
- Trade Value: Thousands of US dollars (current prices)
- Download format : Select CSV or Excel format
  
**Description**

- Export data for two HS product categories:
  - **HS72** – Iron and steel  
  - **HS73** – Articles of iron or steel
- Reporter: EU countries, UK, USA, Norway,Switzerland, Iceland, United States, Canada, Japan, South Korea, China, Australia, New Zealand .
- Partner: *World* (total exports to the world).
- Measure: export value in **thousands of current US dollars**.


Key variables in the cleaned dataset:

- `ReporterName` – exporting country
- `ReporterISO3` – ISO3 country code
- `Year` – calendar year
- `ProductCode` – HS code (72 or 73)
- `TradeValue_USD_1000` – export value (1,000 US$)
- `Log_Trade_Value` – `log(TradeValue_USD_1000 + 1)`

**Processing steps**

- Harmonised country names to ISO3 codes (`ReporterISO3`) for merging.

---

### 3. Merge

The final analysis dataset (`final_data`) is built by:

1. Merging the **carbon-pricing panel** and the **trade data** on  
   `ReporterISO3` (country) and `Year`.
2. Keeping only observations with a non-missing carbon price (`All_Price_Mean`) for the main regressions.
3. Defining one row per **country × year × product (HS72 or HS73)**.


## Analysis Period
**2022-2024** (3 years)

The final analysis dataset contains:
- 36 countries (reporters)
- 2 product categories (HS72, HS73)
- 3 years (2022-2024)
- 216 total observations

This time period reflects the availability of:
1. Comprehensive trade data from UN Comtrade/WITS
2. Matched carbon pricing data from World Bank

This merged dataset is used for all descriptive statistics, visualisations, and regression models in the project.

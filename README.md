# ACS Demographics by ZIP Code — 14-Year Sample Data + Notebooks

Fourteen years (2011–2024) of US Census **American Community Survey 5-Year
Estimates**, queryable per ZIP code through the [ZIP Codes API](https://api.zip-codes.com/docs).
This repo contains:

- **[`acs_shifts_by_zip.ipynb`](acs_shifts_by_zip.ipynb)** — pull a 14-year demographic
  time series for any US ZIP in ~10 lines of Python (runs as-is with the public demo
  key, no signup)
- **[`radius_spatial_acs.ipynb`](radius_spatial_acs.ipynb)** — coverage-weighted demographics
  inside a radius: `/radius` spatial mode intersects ZIP boundary polygons and weights ACS
  aggregates by each ZIP's `pct_inside` (population within 10 mi of a point, 2011 vs 2024),
  no GIS software required
- **[`data/nevada_acs_by_zip_2011_2024.csv`](data/nevada_acs_by_zip_2011_2024.csv)** —
  a complete worked sample: every Nevada ZCTA × every year × ten headline indicators,
  with margins of error (2,474 rows)

## Why this exists

ZIP-level longitudinal analysis usually means downloading Census data profile tables
year by year, reconciling variable codes that shift position between vintages, and
handling the 2020 `GEO_ID` format change. The API does that normalization once, server-side:
one call returns any combination of years (2011–2024) and profiles (DP02 social,
DP03 economic, DP04 housing, DP05 demographic — ~500 fields per ZIP per year), each
value with its margin of error.

## Sample CSV data dictionary

One row per `(zcta, year)`. Blank cells are Census-suppressed values or fields not
published in that year's profile (kept blank rather than zero-filled — they are
genuinely unknown, not zero).

| Column | Description |
|---|---|
| `zcta` | 5-digit ZIP Code Tabulation Area |
| `year` | ACS 5-Year vintage (2011–2024) |
| `population_est` / `population_moe` | Total population (DP05) |
| `median_age_est` / `median_age_moe` | Median age in years (DP05) |
| `housing_units_est` / `housing_units_moe` | Total housing units (DP05) |
| `households_est` / `households_moe` | Total households (DP02) |
| `bachelors_or_higher_pct` / `bachelors_or_higher_pct_moe` | % of adults 25+ with a bachelor's degree or higher (DP02) |
| `median_household_income_est` / `median_household_income_moe` | Median household income, inflation-adjusted dollars of that vintage (DP03) |
| `unemployment_rate_pct` / `unemployment_rate_pct_moe` | Unemployment rate, % of civilian labor force (DP03) |
| `below_poverty_pct` / `below_poverty_pct_moe` | % of all people below the poverty line (DP03) |
| `median_home_value_est` / `median_home_value_moe` | Median value of owner-occupied units (DP04) |
| `median_gross_rent_est` / `median_gross_rent_moe` | Median gross rent (DP04) |

## Data notes (read before analyzing)

- **ZIP vs ZCTA**: rows are keyed by Census ZCTA, the standard polygon approximation
  of a ZIP code. Most ZIPs map cleanly; PO-Box-only and very small ZIPs do not have
  ZCTAs. (This is true of *any* ZIP-level Census analysis, not specific to this API.)
- **ZCTA universe change**: 2011–2020 vintages use 2010-Census ZCTAs (175 in Nevada);
  2021+ use 2020-Census ZCTAs (181). A handful of ZCTAs therefore exist in only part
  of the series.
- **Margins of error matter**: ACS 5-Year values are survey estimates. Small ZCTAs
  have wide MOEs — check the `_moe` column before claiming a trend.
- **Income/value dollars are not inflation-adjusted across vintages** — each vintage
  reports in its own inflation-adjusted dollars. Adjust before comparing dollar
  amounts across years.

## Run the notebook

```bash
pip install requests pandas matplotlib jupyter
jupyter notebook acs_shifts_by_zip.ipynb
```

Works immediately for the demo ZIPs (90210, 10001, 10118, 32504, 00601, 96950,
33139, 60601, 98101, 30301). For any other ZIP,
[get a free API key](https://www.zip-codes.com/api/signup) — 2,500 credits/day,
no credit card — and replace `KEY`.

## About the API

- **Python client:** `pip install zip-codes-api` — the notebooks above use raw `requests`
  for clarity, but the [official client](https://github.com/ZIP-Codes-API/zip-codes-api-python)
  has `acs_timeseries()` and `radius_dataframe()` helpers that return pandas DataFrames directly.
- Docs: <https://api.zip-codes.com/docs> · OpenAPI: <https://api.zip-codes.com/v2/openapi.json>
  · LLM-ready reference: <https://api.zip-codes.com/llms-full.txt>
- Also covers: Canadian FSA/postal codes at parity, radius search (cross-border),
  US address validation with ZIP+4, congressional / state-legislative / school
  district lookups
- Researchers, journalists, students, and nonprofits: extended credit grants are
  available in exchange for a data acknowledgement — email <jharris@zip-codes.com>

## Source and license

Underlying demographic data: **US Census Bureau, American Community Survey 5-Year
Estimates** (public domain). The sample CSV is released under
[CC0](https://creativecommons.org/publicdomain/zero/1.0/); notebook code under MIT.

Suggested acknowledgement:

> Demographic data: US Census Bureau ACS 5-Year Estimates, accessed via ZIP Codes API
> (Zip-Codes.com), https://www.zip-codes.com/api/

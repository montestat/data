# SDG Platform — Montenegro (montestat/data)

This repo is the data layer for Montenegro's Open SDG platform. The git root is `data/` (not the parent `open-sdg/` folder).

## Repository Layout

```
data/
  data/            ← CSV files, one per indicator (indicator_X-Y-Z.csv)
  indicator-config/ ← YAML per indicator (X-Y-Z.yml)
  meta/            ← YAML metadata per indicator (X-Y-Z.yml)
  translations/
    cnr/           ← Montenegrin translations
      data.yml     ← all CSV column values (units, categories, Sex, Age, Location)
      sources.yml  ← data source labels
    en/            ← English translations (same structure)
      data.yml
      sources.yml
```

## CSV Format

Every CSV must follow this column order:

```
Year,Units,[optional disaggregations],Value
```

Optional disaggregation columns (include only what the indicator uses):
- `Sex` — values: `Male`, `Female`, `BOTHSEX` (must match translation keys exactly)
- `Age` — values: `allages`, `aged 5-17 years`, etc.
- `Location` — values: `allarea`, `urban`, `rural`, or named locations
- `Category` — for multi-series indicators (see category key convention below)
- `Convention` — specific to 12.4.1

Every value in every column (except Year and Value) **must have a matching key** in both `translations/cnr/data.yml` and `translations/en/data.yml`.

## Category Key Convention

Category values in CSVs follow the **indicator-prefixed format**:

```
{indicator_number_with_underscores}_cat_{N}
```

Examples: `1_1_1_cat_1`, `4_1_2_cat_2`, `5_5_1_cat_1`, `10_5_1_cat_2`

Some indicators also use named categories: `4_7_1_national_policies`, `13_2_1_ndc`, `13_3_1_cat_1`.

Never use bare names like `national_policies` — always prefix with the indicator number.

## Translation Rules

**All** non-Year/Value column values must be in **both** `cnr/data.yml` AND `en/data.yml`.

Common keys already present (do not re-add):
- Units: `%`, `yes_no`, `points`, `score`, `score_1_10`, `index`, `ratio`, `tonnes`, `kg_per_usd`, `thousands_eur`, `1000_tons`, `1000_passengers`, `per 1000 life births`
- Location: `allarea`, `urban`, `rural`
- Sex: `Male`, `Female`, `BOTHSEX`, `MALE`, `FEMALE`
- Age: `allages`, `aged 5-17 years`, `aged 5-11 years`, `aged 12-14 years`, `aged 15-17 years`
- General: `total`, `upper_high_school`, `local_road_traffic`

When adding new translation keys:
1. Append to end of `translations/cnr/data.yml`
2. Append to end of `translations/en/data.yml`
3. YAML keys with special characters must be quoted: `'%': 'procenat'`

## Source References

Sources referenced in `meta/` YAMLs (`sources.X.organisation`) must exist in both `translations/cnr/sources.yml` and `translations/en/sources.yml`.

Current sources: `Monstat`, `IJZCG`, `MinEducation`, `MinInterior`, `MinJustice`, `CentralBank`, `MinScience`, `MinForeignAffairs`, `MinTourism`, `EPA`, `MinAgriculture`, `MinFinance`, `MUP`, `biotechnical_institute`, `health_insurance_fund`, `MinHumMinRights`, `MinLaborSoc`, `World_Travel_Tourism_Council_WTTC`, `CBCG`, `EKIP`, `IBM`, `UNECE`, `OECD`, `FAO`, `WHO`, `responsible_institution`, `MORT`, `UNHCR`

## Indicator Config

`indicator-config/X-Y-Z.yml` controls display settings. Key field:
- `reporting_status: notstarted` → change to `reporting_status: complete` when data is added

## UNECE Data Source

Many indicators are sourced from [UNECE SDG platform](https://w3.unece.org/SDG/en/):
- Each indicator has a numeric `id` (e.g. `?id=145`)
- Use WebFetch to extract Montenegro data: search for "Montenegro" or "MNE"
- Always cross-check new values against existing rows to confirm correct country

Known UNECE indicator IDs used so far:
| SDG | UNECE id | Description |
|-----|----------|-------------|
| 2.1.2 | 154 | Food insecurity % |
| 2.5.2 | 184 | Local breeds at risk % |
| 5.5.1(a) | 142 | Women in national parliament % |
| 5.5.1(b) | 141 | Women in local government % |
| 6.3.2(a) | 136 | Water bodies good quality % (total) |
| 6.3.2(c) | 138 | Open water bodies good quality % |
| 6.3.2(d) | 139 | River water bodies good quality % |
| 6.4.1(a) | 155 | Water-use efficiency total |
| 6.4.1(b) | 161 | Water-use efficiency agriculture |
| 6.4.1(c) | 162 | Water-use efficiency industry |
| 6.4.1(d) | 163 | Water-use efficiency services |
| 6.5.1 | 232 | Integrated water management % |
| 7.1.1 | 159 | Electricity access % |
| 8.8.2 | 208 | Labour rights compliance score |
| 8.10.2 | 211 | Adults with bank account % |
| 10.5.1(a) | 212 | Non-performing loans net of provisions % |
| 10.5.1(b) | 213 | Non-performing loans to total gross loans % |
| 12.2.2(a) | 145 | DMC per unit of GDP (kg/USD) |
| 12.2.2(b) | 54 | DMC per capita (tonnes) |

## Yes/No Indicators

Binary policy indicators use `yes_no` as unit and `1` for YES, `0` for NO.

## Education Policy Scale (4.7.1, 13.3.1)

UNESCO 0–4 integration scale:
- 4 = fully integrated
- 3 = systematically integrated  
- 2 = partially integrated
- 1 = minimally integrated
- 0 = not integrated

## Git Workflow

Branch: `develop` → merge to `master`

Push requires embedding the username due to Windows Credential Manager caching:
```
git push https://nikolabetf@github.com/montestat/data.git develop
git push https://nikolabetf@github.com/montestat/data.git master
```

Typical commit workflow:
```
git add <files>
git commit -m "message"
git push https://nikolabetf@github.com/montestat/data.git develop
git checkout master && git merge develop
git push https://nikolabetf@github.com/montestat/data.git master
git checkout develop
```

## Common Pitfalls

1. **Edit tool requires reading first** — always Read a file before using Edit on it
2. **Category key mismatch** — CSV values must exactly match translation keys including the indicator prefix
3. **Duplicate rows** — check for duplicate year+category combinations before adding data; prefer newer/UNECE data
4. **Column count** — every data row must have the same number of columns as the header
5. **Academic year convention for education** — use the end year (2021/22 → 2022)
6. **UNECE minor revisions** — UNECE sometimes revises old values slightly; don't update existing rows, just append new years
7. **`%` key in YAML** — must be quoted: `'%': 'procenat'`

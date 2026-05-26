# TaxLocus Open Sales-Tax Dataset

Free, weekly-updated US sales-tax **rate tables** — the flat data behind
[TaxLocus](https://taxlocus.com), released as an open dataset for the community.

**Data as of: 2026-05-19** · Refreshed **weekly** from the live TaxLocus corpus.

## What this is

A clean, flat snapshot of US sales-tax rates and rules:

- **State base rates** for all sales-tax states.
- **Local jurisdiction rates** — ~14,000 counties, cities, parishes, boroughs,
  transit and special districts.
- **Economic-nexus thresholds** — the per-state remote-seller registration
  triggers.
- **Taxability matrix** — state × product-category taxable/exempt treatment.

Both **CSV** and **JSON** are provided for every table.

## What this is *not*

This is the rate data only. The **address-level lookup API**, the polygon
**geometry** that resolves an address to its jurisdiction stack, and the
**nexus tracking tools** are the paid product at
**[taxlocus.com](https://taxlocus.com)**. This dataset will tell you the rate
*for a named jurisdiction*; it will not tell you *which* jurisdictions a given
street address falls in.

## Files & schema

All files live in [`data/`](data/).

### `state_rates` — statewide base rates

| column | description |
|--------|-------------|
| `state` | 2-letter state code |
| `rate`  | statewide base sales-tax rate (decimal, e.g. `0.0625`) |

### `jurisdiction_rates` — local jurisdiction rates

| column | description |
|--------|-------------|
| `state` | 2-letter state code |
| `jurisdiction_type` | `county`, `city`, `parish`, `borough`, `transit`, or `special_district` |
| `name` | jurisdiction name |
| `fips_code` | Census FIPS code, where available (may be blank) |
| `rate` | local rate for the jurisdiction (decimal) |

### `nexus_thresholds` — economic-nexus thresholds

| column | description |
|--------|-------------|
| `state` | 2-letter state code |
| `sales_threshold` | USD sales threshold (may be blank) |
| `transaction_threshold` | transaction-count threshold (may be blank) |
| `logic` | `sales_only`, `transactions_only`, `sales_or_count`, or `sales_and_count` |
| `measurement_period` | `prior_calendar_year`, `current_or_prior_cy`, `rolling_12_month`, or `prior_4_quarters` |

### `taxability` — state × category taxability matrix

| column | description |
|--------|-------------|
| `state` | 2-letter state code |
| `category` | product-category code (e.g. `food.grocery`, `clothing.general`) |
| `taxable` | `True`/`False` |
| `treatment` | `taxable`, `exempt`, `reduced_rate`, `exempt_with_cert`, or `partial` |

### `manifest.json`

Generation date and per-file row counts for the current snapshot.

## Payroll-tax open data (new — Plan 14)

Reference tables for **US payroll tax** — federal FICA/FUTA, state personal
income tax (PIT) and SUTA wage bases, and **local income tax** where it
exists (PA, OH, MD, IN, NYC, etc.). Same shape + CC-BY-4.0 licence; every
row carries a `citation_url` to the authoritative source (IRS, state DOR,
state UI agency) so any value can be verified or refreshed against the
source. This is **rate / reference data only** — not a withholding-tax
calculation engine, not the per-employer SUTA experience rate (which is
state-assigned and not lookupable). Consumers compute their own withholding
and take liability for it.

### `payroll_federal_rates`
FICA (SS + Medicare + Additional Medicare 0.9% > $200k) and FUTA rates,
wage bases, and credit max by year.

### `payroll_state_pit` — state personal income tax (wage withholding)
| column | description |
|--------|-------------|
| `year` | tax year |
| `state` | 2-letter state code |
| `tax_type` | `none` (no wage income tax) / `flat` / `progressive` |
| `flat_rate` | flat rate (when `tax_type='flat'`) |
| `top_marginal` | top marginal rate (when `tax_type='progressive'`) |
| `bracket_summary` | optional compact JSON of brackets |
| `supplemental_rate` | supplemental (bonus) withholding rate where published |
| `citation_url` | state DOR / withholding guide |
| `notes` | per-state nuance |

### `payroll_state_suta` — state unemployment-insurance wage base + rate range
| column | description |
|--------|-------------|
| `year` | tax year |
| `state` | 2-letter state code |
| `wage_base` | annual SUTA wage base per employee |
| `new_employer_rate` | new-employer assigned rate (when published) |
| `min_rate` / `max_rate` | bounds of the experience-rate range |
| `citation_url` | state UI agency |

Per-employer SUTA rates are NOT included — each employer's rate is
experience-rated and assigned by the state UI agency annually.

### `payroll_local_income_tax` — city / county / municipal income tax
| column | description |
|--------|-------------|
| `year` | tax year |
| `state` | 2-letter state code |
| `jurisdiction_type` | `city` / `county` / `municipality` / `school_district` / `township` / `borough` |
| `jurisdiction_name` | e.g. `Philadelphia`, `Anne Arundel County`, `New York City` |
| `geoid` | FIPS / place GEOID when known |
| `resident_rate` / `non_resident_rate` | withholding rates |
| `courtesy_withholding` | whether the state publishes a courtesy-withholding flag |
| `citation_url` | state DOR / local agency |

Coverage rolls out in phases — PA, MD, IN, NYC, DC first; OH / MI / KY / MO / DE next.

### `payroll_reciprocity` — state income-tax reciprocity agreements
| column | description |
|--------|-------------|
| `state_a` / `state_b` | the two states |
| `direction` | `mutual` or `a_to_b` (one-way) |
| `citation_url` | state DOR |

Reciprocity exempts the non-resident state from withholding when an employee
files the exemption form (e.g., PA REV-419, MD MW507).

## License & attribution

This dataset is licensed under the **Creative Commons Attribution 4.0
International (CC-BY-4.0)** license — see [`LICENSE`](LICENSE).

You are free to use, share, and adapt the data, including commercially,
**provided you give credit**. Required attribution:

> Sales-tax data by **TaxLocus** — https://taxlocus.com

## Disclaimer

This dataset is provided for **informational purposes only and is not tax
advice**. Sales-tax rates and rules change frequently, special districts and
local boundaries are complex, and this snapshot may contain errors or omissions.
Always verify against the relevant state Department of Revenue before relying on
it for filing or remittance. TaxLocus makes no warranty as to accuracy,
completeness, or fitness for any purpose.

## Need address-level accuracy?

The free dataset gives you rates by named jurisdiction. To resolve a real
street address to its exact jurisdiction stack — with geometry, product
taxability, and economic-nexus tracking — use the TaxLocus API at
**[taxlocus.com](https://taxlocus.com)**.

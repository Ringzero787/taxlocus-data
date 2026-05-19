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

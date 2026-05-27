# Payroll-tax open data — coverage, gaps, and disclaimers

> **Read this before relying on this data for withholding.** TaxLocus
> payroll-tax data is informational rate reference only. It is **NOT** tax
> or legal advice, **NOT** a set of withholding tables, **NOT** a calculation
> engine. Errors in payroll withholding create Form 941 employer penalties —
> the consequence is higher than a sales-tax miscalculation.

This file documents what's in the dataset, what's deliberately left out, and
the state-by-state gaps you should know about.

---

## What's in

### Federal — `payroll_federal_rates`
Two years (2024, 2025) of:
- **FICA Social Security** 6.2% to the annual SS wage base (employee + employer)
- **FICA Medicare** 1.45% no cap (employee + employer)
- **Additional Medicare** 0.9% above $200k single-filer threshold (employee only)
- **FUTA** 6.0% gross, 5.4% state credit, **0.6% effective** on $7,000 wage base

### State personal income tax — `payroll_state_pit`
Top-line rate (or no-tax flag) for **all 50 states + DC** for 2025. Each row carries a `tax_type`:
- `none` — no wage income tax (NV, TX, FL, WA, SD, WY, TN, AK; NH interest-only)
- `flat` — single rate (CO, IL, IN, MI, NC, PA, UT, etc.)
- `progressive` — `top_marginal` is the top bracket; use the state's actual brackets for accurate withholding

### State unemployment wage base — `payroll_state_suta`
2025 taxable wage base per state (e.g., WA $72,800, OR $54,300, FL $7,000). **The tax RATE itself is not published** — SUTA is experience-rated per employer and the state mails each employer its rate annually.

### State-to-state reciprocity — `payroll_reciprocity`
28 reciprocal income-tax pairs (IL/IN, MD/VA/PA/WV/DC, NJ/PA, etc.). Workers who live in one state and work in the other only owe tax to their resident state, given the proper exemption certificate.

### Local income tax — `payroll_local_income_tax`
**3,662 rows across 8 states** — the differentiated piece of the dataset:

| State | Rows | Source |
|---|---:|---|
| PA | 2,627 | PA DCED EIT register — every PSD with municipal + school-district resident rate and municipal non-resident rate |
| OH | 890 | Union of RITA (467) + CCA (46) + Ohio DOR canonical PDF (377 self-administered cities/JEDDs) |
| IN | 92 | IN DOR Departmental Notice #1 |
| MD | 24 | 23 counties + Baltimore City piggyback rates (resident only — MD doesn't tax non-residents at the county level) |
| MI | 24 | All 24 Michigan city-income-tax cities |
| NY | 2 | NYC + Yonkers |
| MO | 2 | Kansas City + St. Louis |
| DE | 1 | Wilmington |

Every row carries `as_of` (last refresh date) and `citation_url` (the authoritative source).

---

## What's NOT in — and why

| Excluded | Why |
|---|---|
| **Withholding calculation engine** | We publish rates. Translating a W-4 + pay period + filing status + bracket structure into a per-paycheck withholding amount is its own large liability surface — consumers apply their own engine (Symmetry Payroll Point, Vertex Payroll, in-house) to our rates. |
| **Full state PIT bracket tables** | `payroll_state_pit` carries the top-line rate. Maintaining full brackets across 30+ progressive states is its own ongoing burden; we link the official withholding tables in `citation_url`. |
| **Per-employer SUTA tax rates** | Experience-rated. The state mails each employer their specific rate each year. No third party can publish this — only the state knows it. |
| **State-level disability / paid family leave** | CA SDI, NY PFL, NJ TDI, RI TDI, HI TDI, WA PFML, OR PFMLI, CO FAMLI, MA PFML, MD FAMLI — varying shapes (employer-only / split / employee-only) with their own wage bases. Roadmap candidate. |
| **Employer-side local payroll taxes** | NJ Newark payroll tax, CA SF gross-receipts/payroll-expense, Pittsburgh payroll tax, Wilmington head tax — employer-side compliance, separate problem domain. |
| **Garnishments / child support / wage attachments** | Court orders, not tax rates. |

---

## State-by-state gaps

### ⚠️ Local income tax exists but we don't have it yet

| State | What exists | Why not yet | Workaround |
|---|---|---|---|
| **KY** | ~125 cities + ~80 counties — Kentucky occupational license fees | No central registry. KOLA (kola.us) is a parked redirect; revenue.ky.gov publishes no consolidated table. Coverage requires per-jurisdiction scraping of ~200 city/county tax-office sites. | Check each jurisdiction's tax office directly. |
| **IA** | ~280 school districts — Iowa school district surtax (% of state PIT) | IA DOR publishes annually as a PDF; future polish item. | Iowa DOR Iowa School District Surtax List, annually. |
| **OR** | Multnomah County Preschool For All (1.5%/3.0% above $125k/$250k) and Portland Metro Supportive Housing Services (1% above $125k/$250k) | Single-metro scope; future polish. | Multnomah County / Metro tax pages. |
| **AL** | Birmingham, Bessemer, Gadsden, Macon County occupational tax | 4 jurisdictions; future polish. | Each city/county finance department. |
| **CO** | Denver, Aurora, Glendale, Greenwood Village, Sheridan occupational privilege tax — **flat $ per employee per month, not a percent** | Structurally different from a percentage rate; needs its own schema column. Future polish. | Each city's OPT page. |
| **WV** | Charleston, Huntington, Wheeling, Parkersburg, Weirton city service fees — flat $/week per employee | Same shape as CO; future polish. | Each city's finance office. |
| **KS** | Intangibles tax (on interest income, not wages) | Limited applicability; not a payroll matter. | KS DOR — limited. |
| **NJ-Newark** | Newark payroll tax | Employer-side, not employee withholding. Out of scope. | City of Newark — employer compliance. |

### ✅ No local income tax at all — correctly absent, not a gap

AK · AZ · AR · CA · CT · FL · GA · HI · ID · IL · LA · ME · MA · MN · MS · MT · NE · NV · NH · NM · NC · ND · OK · RI · SC · SD · TN · TX · UT · VT · VA · WA · WI · WY · **DC** (DC income tax is state-level, already in `payroll_state_pit`)

That's 34 states + DC. They have no local wage tax; nothing exists to publish.

---

## Refresh cadence

Every Tuesday 06:00 UTC the auto-refreshable scrapers re-run against upstream sources:
- **PA DCED EIT** — picks up any rate change in the official PSD register
- **OH DOR canonical PDF** — re-scrapes the parent page to find the current rates PDF
- **OH RITA** — JSON endpoint
- **OH CCA** — Bootstrap-tabbed HTML
- **IN DOR Departmental Notice #1** — PDF re-parsed

The hand-curated tables (federal FICA/FUTA, state PIT top-line, state SUTA wage bases, reciprocity, MD piggyback, MI cities, NYC/Yonkers/KC/STL/Wilmington) change once per year and are re-cut by hand each January.

Every row carries an `as_of` column. The `manifest.json` at the dataset root has the publish timestamp.

---

## Disclaimer

TaxLocus payroll-tax data is **informational rate reference only**. **NOT TAX OR LEGAL ADVICE. NOT WITHHOLDING TABLES. NOT A CALCULATION ENGINE.** Errors in payroll withholding create Form 941 employer penalties — the consequence is higher than a sales-tax miscalculation.

Before relying on any rate for actual withholding:
1. Verify the value against the linked `citation_url`.
2. Apply it correctly to wages in your own payroll engine, accounting for the employee's W-4, filing status, pay frequency, and any state bracket structure.
3. Consult a qualified payroll-tax professional or tax attorney for compliance questions.

The TaxLocus [Terms of Service](https://taxlocus.com/terms) apply, including the 90-day liability cap.

Corrections welcome at [github.com/Ringzero787/taxlocus-data](https://github.com/Ringzero787/taxlocus-data).

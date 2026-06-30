# HIV/AIDS Commodity Stock on Hand Dashboard
### ECEWS ACE-5 Programme — Lagos State, Nigeria

---

## Project Overview

This repository contains interactive weekly dashboards tracking HIV/AIDS commodity stock on hand (SOH) across **55 health facilities** in Lagos State, Nigeria, under the **ECEWS ACE-5** programme (Achieving Epidemic Control — Phase 5, funded by PEPFAR/USAID).

The dashboards are designed to support supply chain decision-making by the ACE-5 Lagos State team, providing facility-level visibility into inventory levels for antiretrovirals (ARVs), opportunistic infection (OI) drugs, diagnostic test kits, and laboratory consumables.

---

## Programme Context

| Field | Detail |
|---|---|
| Programme | ECEWS ACE-5 (Achieving Epidemic Control — Phase 5) |
| Implementing Partner | ECEWS (Excellence & Friends Management Care Centre) |
| Funder | PEPFAR / USAID |
| State | Lagos State, Nigeria |
| Health Facilities | 55 reporting sites |
| Reporting System | DHIS2 (District Health Information System 2) |
| Reporting Cadence | Weekly (Wednesday cutoff, SOH as at physical count) |

---

## Commodity Categories

Each dashboard tracks **up to 41 commodity lines** grouped into three categories:

### ARVs & OIs (20–21 lines depending on week)
TDF/3TC/DTG, TDF/3TC, ABC/3TC/DTG, ABC/3TC 600mg, ATV/r, AZT/3TC, ABC/3TC 120/60mg (60t), ABC/3TC 120/60mg (30t), AZT 50mg/5ml, NVP 50mg/5ml, DTG 10mg, DTG 50mg, INH 100mg, INH 300mg, DVR/r, Cotrimoxazole 960mg, Cotrimoxazole 120mg, Liposomal Amphotericin B, Flucytocine, Fluconazole 200mg, 3HP (Isoniazid/Rifapentine)

### Diagnostic Test Kits (10 lines)
Standard Q, Determine, Unigold, Stat Pak, DBS, HIV-Syphilis, HIVST, Visitec CD4, TB-LAM, CrAg-LFA

### Lab Consumables & Medical Supplies (10 lines)
EDTA Bottles, GeneXpert-EID, Male Condoms, Female Condoms, Vacutainer Needles, Pasteur Pipettes, Methylated Spirit, Gloves, Cryovial Tubes, Cotton Wool

---

## Column Schema Evolution

The DHIS2 export format changed across the reporting period:

| Week(s) | Columns | Key Changes |
|---|---|---|
| W18–W19 | 36 | Baseline — 17 ARV/OI, 9 kits, 10 lab |
| W20 | 40 | Added INH 100mg, Liposomal Amphotericin B, Flucytocine, Standard Q Test Kits |
| W21 | 41 | Added 3HP (Isoniazid/Rifapentine) |
| W22–W24, W26 | 40 | Returned to W20 structure (no 3HP) |

> **Note:** Week 25 data was not received. The series jumps from W24 to W26.

---

## Repository Structure

```
/
├── README.md                          ← This file
│
├── dashboards/                        ← Standalone weekly SOH dashboards
│   ├── W18_2026_Dashboard.html
│   ├── W19_2026_Dashboard.html
│   ├── W20_2026_Dashboard.html
│   ├── W21_2026_Dashboard.html
│   ├── W22_2026_Dashboard.html        ← Includes corrections (Sango PHC, Ajeromi, Orile Agege)
│   ├── W23_2026_Dashboard.html
│   ├── W24_2026_Dashboard.html
│   └── W26_2026_Dashboard.html
│
└── data-projections/                  ← Supplementary analysis files
    └── W24_Determine_Kits_SOH_Projection.xlsx
```

---

## Dashboard Features

Each `.html` file is a **fully self-contained, standalone dashboard** — no internet connection, server, or external dependencies required. Open any file directly in a web browser.

### Four Interactive Tabs

| Tab | Description |
|---|---|
| **Overview** | KPI cards (total units by category), top 10 facilities bar chart, category donut chart, top commodities by category |
| **Commodity Explorer** | Filter by category or search by name; click any commodity to see all 55 facilities ranked by SOH, with a bar chart and list of zero-stock facilities |
| **Facility Explorer** | Click any facility to see its full inventory across all commodity lines, with mini bar charts per commodity |
| **Heatmap** | Full 55 × 40 matrix with per-column colour scaling (white → dark green), filterable by category |

### Interactivity
- Tab switching via event listeners (no page reload)
- Commodity → Facility cross-navigation: clicking a facility name in the Commodity Explorer jumps to its full profile in the Facility Explorer
- Search and filter controls on Commodity and Facility Explorer panels
- Heatmap category filter buttons

---

## Tech Stack

| Component | Detail |
|---|---|
| **Format** | Standalone HTML (no framework, no CDN dependencies) |
| **Styling** | Custom CSS with CSS variables; greyscale theme (`#e8e8e8` background, `#f2f2f2` panels) |
| **Typography** | Google Fonts — DM Mono (monospace body) + Fraunces (serif headings), loaded via `@import` |
| **Visualisation** | Vanilla JavaScript — HTML5 Canvas (donut chart), DOM-rendered bar charts and heatmap |
| **Data** | Embedded JSON (`var D = {...}`) injected at build time from DHIS2 weekly exports |
| **Interactivity** | `data-*` attribute–based event listeners (no `onclick` attributes — avoids quote-escaping issues) |
| **Build pipeline** | Python (openpyxl for Excel output; custom HTML builder using string concatenation, not f-strings in JS sections) |
| **Data source** | DHIS2 weekly exports (tab-separated), zero-filled before processing |

---

## Supplementary Analysis

### `W24_Determine_Kits_SOH_Projection.xlsx`
A facility-level projection of **Determine Test Kit** stock after a distribution round conducted during the W24 reporting period.

| Column | Description |
|---|---|
| W24 SOH | Stock on hand as reported at Week 24 |
| Quantity Supplied | Units distributed to each facility |
| Projected SOH | Formula-calculated sum (W24 SOH + Supply) |

**Summary:** 22,456 units (W24 SOH) + 12,100 units (supplied) = **34,556 projected units** across 54 of the 55 facilities. Federal Medical Centre Ebute-Metta excluded (not part of the ACE-5 55-facility network).

---

## Data Quality Notes

| Facility | Note |
|---|---|
| Lagos State Mainland Hospital | Determine and Unigold values — verify against physical tally in early weeks |
| Orile Agege General Hospital | Consistently very high EDTA Bottles (11,750–12,100) and W21 Cryovial Tubes (12,100); treat as known anomaly pending physical count verification |
| NIMR | Large ARV values appearing in W23–W24 — suspected reporting correction or bulk receipt; verify before accepting as SOH |
| Sango Primary Health Centre | Reported all-zero in W22 first submission; corrected to full reporting in updated dataset |
| Massey Street Childrens Hospital / Oyems Hospital | Apostrophes removed from facility names in source data to prevent JavaScript string-parsing errors |

---

## Standard Data Processing Protocol

For each new week before building the dashboard:

1. Fill **zeros** in all blank cells (prevents tab-parsing ambiguity)
2. Remove **apostrophes** from facility names (Massey Street Childrens Hospital, Oyems Hospital)
3. Strip `la ` DHIS2 prefix from facility names where present
4. Assert **column count** matches expected schema for that week (36 / 40 / 41)
5. Spot-check column sums against the `TOTAL` row
6. Run output assertions: no escaped quotes, no `onclick=` attributes, exactly one `<script>` block

---

## Contact

**Programme:** ECEWS ACE-5, Lagos State  
**Data Management:** Supply Chain / M&E Team  
**Repository maintained by:** Akan

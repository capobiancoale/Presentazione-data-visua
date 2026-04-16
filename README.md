# Water Insecurity in the United States: County-Level Analysis

## Overview

This is an interactive presentation analyzing water insecurity across the United States at the county level using American Community Survey (ACS) 5-Year Estimates data. The analysis focuses on the prevalence of households lacking complete plumbing facilities as a key indicator of water infrastructure deprivation.

**Project Authors:** Francesco Pirovano · Elena La Chiusa · Alessio Capobianco  
**Institution:** Università Cattolica del Sacro Cuore  
**Data Source:** TidyTuesday 2025, Week 4

---

## Project Structure

This Quarto presentation (`codice 1`) is a **reveal.js** web-based slideshow that combines data analysis, visualization, and storytelling to examine water insecurity patterns across the U.S.

### Key Files
- `codice 1` — Main Quarto presentation file (R + markdown)

---

## Research Questions

The analysis addresses three core research questions:

1. **RQ1: Severity** — Which counties and states are most severely affected by plumbing insecurity?
2. **RQ2: Distribution** — How is the problem distributed geographically and demographically?
3. **RQ3: Trajectory** — What changed in water infrastructure between 2022 and 2023?

---

## Data

### Source
- **U.S. Census Bureau American Community Survey (ACS)**
- **Type:** 5-Year Estimates
- **Years Analyzed:** 2022 and 2023 (rolling windows: 2018–2022 and 2019–2023)
- **Coverage:** All 50 U.S. states + Washington, D.C.
- **Granularity:** County level (FIPS codes)
- **Total Records:** 3,100+ counties analyzed

### Key Variables
| Variable | Description |
|----------|-------------|
| `geoid` | County FIPS code (unique identifier) |
| `name` | County + State name |
| `total_pop` | Total county population |
| `plumbing` | Number of households lacking complete plumbing |
| `percent_lacking_plumbing` | Percentage of households lacking plumbing |

### Definition
**Complete plumbing** = simultaneous access to hot/cold running water, a flush toilet, and a bathtub or shower.

---

## Data Cleaning & Processing

All data cleaning follows the **DRY (Don't Repeat Yourself) principle** via a reusable `clean_water()` function:

```r
clean_water <- function(df) {
  df |>
    mutate(
      rate   = percent_lacking_plumbing / 100,     # Convert to proportion (0–1)
      state  = str_extract(name, "(?<=, ).+$"),    # Extract state name
      county = str_remove(name, ",.*$")             # Extract county name
    ) |>
    filter(
      !is.na(rate), !is.na(total_pop),
      total_pop > 0,
      !state %in% EXCLUDE_TERRITORIES               # Remove US territories
    )
}

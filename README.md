# COVID-19 Mortality Analysis in R

A statistical analysis of U.S. COVID-19 mortality rates (2020–2023) by age, sex, region, and year, using count regression, mixed-effects models, and multiple imputation for suppressed data.

## Overview

This project models COVID-19 mortality rates using more than 138,000 rows of CDC provisional death counts, combined with age- and sex-specific Census population estimates. It began as a STAT 204 (graduate) course project and was later extended independently to correct the rate denominators, account for correlation within states, and handle CDC-suppressed counts.

The research question: **How are age, sex, region, and year associated with COVID-19 mortality rates?**

## Key Findings

Estimates are adjusted mortality rate ratios (RR) with 95% confidence intervals from a negative binomial mixed model with state random intercepts, pooled across multiply imputed datasets.

- **Age:** The strongest predictor. Compared with ages 65–74, mortality rates were 2.7 times as high at ages 75–84 (RR = 2.70; 95% CI: 2.57–2.85) and 8.8 times as high at ages 85+ (RR = 8.81; 8.36–9.28).
- **Sex:** Males had 53% higher mortality than females of the same age, region, and year (RR = 1.53; 1.49–1.58). The male excess varied by age: it was largest in middle and older adulthood and narrowed at ages 85+.
- **Region:** Compared with the Midwest, mortality was higher in the South (RR = 1.26; 1.05–1.50) and lower in the Northeast (RR = 0.79; 0.64–0.97). The West did not clearly differ (RR = 0.90; 0.75–1.09).
- **State variation:** States differed substantially beyond what region explains. About 95% of states had mortality rates between 0.64 and 1.57 times their predicted rate, and states in the same region differed widely (for example, New Mexico and Hawaii, both in the West).
- **Year:** Relative to 2020, mortality peaked in 2021 (RR = 1.69; 1.62–1.75), then fell in 2022 (RR = 0.79) and 2023 (RR = 0.19; January–September, annualized).
- **Suppression:** Excluding CDC-suppressed counts underestimated mortality for children under 15, while conclusions for sex, region, and ages 45 and older were essentially unaffected.

## Data Sources

- **CDC:** [Provisional COVID-19 Deaths by Sex and Age](https://data.cdc.gov/NCHS/Provisional-COVID-19-Deaths-by-Sex-and-Age/9bhg-hcku) (National Center for Health Statistics), data as of September 27, 2023. Counts include deaths with COVID-19 listed as an underlying or contributing cause.
- **Census (models):** Vintage 2025 civilian population estimates by state, single year of age, and sex ([SC-EST2025-AGESEX-CIV](https://www.census.gov/data/datasets/time-series/demo/popest/2020s-state-detail.html)), used as age- and sex-specific denominators.
- **Census (maps and regional trends):** Vintage 2023 state total population estimates.
- **Time period:** January 2020 through September 23, 2023.

## Methodology

### Data Preparation
- **Non-overlapping age groups:** Kept 11 age groups that do not overlap (the CDC file also includes overlapping groups such as 0–17 years, which would count the same deaths twice).
- **Person-year denominators:** Joined each state, year, sex, and age group to its own Census population, then multiplied by the fraction of the year covered (about 0.73 for 2023, which ends in September) to obtain person-years at risk.
- **New York City:** Merged with the rest of New York State, since the CDC reports it separately but Census estimates for New York include it.
- **Suppressed counts:** The CDC suppresses counts between 1 and 9 for confidentiality. These 1,136 cells (25% of all state-year-sex-age cells, and 30–54% of cells under age 45) are not missing at random, so they were excluded from the complete-case models and handled with multiple imputation.

### Exploratory Analysis
- Monthly national death counts over time and by calendar month
- Total deaths by age group
- Choropleth maps of crude state mortality rates for each year
- Crude regional mortality trends over time

### Statistical Models
- **Multiple linear regression:** Baseline model of mortality rates; residual diagnostics show violations of linearity, constant variance, and normality.
- **Poisson regression:** Count model with a log person-years offset. A Pearson dispersion ratio and a Cameron–Trivedi test showed strong overdispersion.
- **Negative binomial regression:** Accounts for overdispersion (AIC = 28,997 vs. 152,342 for Poisson). Simulation-based residual diagnostics (DHARMa) indicated adequate overall fit but strong state-level variation.
- **Negative binomial mixed model:** Adds state random intercepts (between-state SD = 0.23 on the log scale; AIC = 28,394). Region standard errors increased about 3.8-fold, correcting the overstated precision of the fixed-effects model, and state-level residual dispersion fell from 5.75 to 1.13.
- **Multiple imputation:** Imputed suppressed counts 40 times from the mixed model's predicted distribution restricted to 1–9 deaths, refit the model on each completed dataset, and pooled the results with Rubin's rules.
- **Age × sex interaction:** Tested with a likelihood ratio test comparing mixed models with and without the interaction.

### Results Presentation
- Forest plot of adjusted rate ratios comparing complete-case and imputed estimates
- Caterpillar plot of state random effects (empirical Bayes estimates)
- Age × sex interaction plot of observed rates on a log scale

## Technologies Used

**Language:** R
**Document generation:** Quarto
**Key packages:**
- Data manipulation: `dplyr`, `tidyr`, `lubridate`
- Visualization: `ggplot2`, `usmap`
- Modeling: `MASS` (negative binomial), `glmmTMB` (mixed models), `AER` (overdispersion test)
- Diagnostics: `DHARMa`
- Model output and tables: `broom`, `broom.mixed`, `knitr`

## Repository Structure

- `data/`: CDC death counts and Census population estimates
- `src/main.qmd`: Full analysis workflow (rendered as `main.html` and `main.pdf`)
- `report/report.qmd`: Written report (rendered as `report.pdf`)

## Getting Started

### Prerequisites
- R (version 4.0 or later)
- Quarto
- R packages:

```r
install.packages(c("dplyr", "tidyr", "lubridate", "ggplot2", "usmap", "MASS",
                   "glmmTMB", "AER", "DHARMa", "broom", "broom.mixed", "knitr"))
```

### Running the Analysis

From the repository root:

```bash
quarto render src/main.qmd       # full analysis workflow
quarto render report/report.qmd  # written report
```

## Limitations

- **Provisional data:** CDC counts were provisional as of September 2023 and may differ from final counts.
- **Imputation assumptions:** The imputation model was fit to complete cases, with state random effects and the dispersion parameter held at their estimates, and assumes the model's distribution restricted to 1–9 deaths describes the suppressed values.
- **Remaining correlation:** State random intercepts capture persistent state differences, but not state-specific changes over time or correlation between neighboring states.
- **Aggregate data:** The analysis uses group-level counts, so results describe populations rather than individual risk.
- **Crude maps and trends:** State maps and regional trends are not age-adjusted; the mixed model's state effects are the age-adjusted comparison.
- **Statistical vs. practical significance:** Large death counts make even small effects statistically significant, so effect sizes and confidence intervals are emphasized over p-values.
- **Omitted factors:** Vaccination coverage, variant prevalence, and county-level variation are not included.

## Future Directions

- Incorporate vaccination rates and variant prevalence
- Model monthly data with smooth time trends and seasonality (e.g., generalized additive models)
- Validate imputed values against reported totals, or model suppressed counts directly with an interval-censored likelihood
- Extend to county-level data for finer geographic resolution

## Authors

- **Original course project (December 2025):** Dane Pearson and Dhriti Avala, STAT 204: Statistical Data Analysis, University of California, Santa Cruz
- **Extended analysis (2026):** Dane Pearson

## References

Data from the CDC National Center for Health Statistics and the U.S. Census Bureau. Full citations are in the report.
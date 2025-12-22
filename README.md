# COVID-19 Mortality Analysis Report

A comprehensive statistical investigation of COVID-19 mortality patterns in the United States (2020-2023) using R and varying regression models.

## Overview

This project analyzes over 138,000 observations from the CDC's provisional death counts to understand how COVID-19 mortality varied across demographic groups, geographic regions, and time periods. The analysis combines exploratory data visualization with multiple statistical modeling approaches to quantify the effects of age, sex, region, and year on COVID-19 mortality rates.

## Key Findings

- **Age**: Strongest predictor of mortality, with adults 85+ experiencing 190x higher death rates than children
- **Sex**: Males showed 37% higher mortality rates than females
- **Regional Variation**: Southern states exhibited 36% higher mortality; Northeast had 24% lower rates compared to Midwest baseline
- **Temporal Trends**: Mortality peaked in late 2020/early 2021, with a 44% annual decline thereafter coinciding with vaccine rollout
- **Seasonality**: Clear seasonal patterns with highest deaths in December/January, lowest in summer months

## Data Sources

- **Primary**: CDC Provisional COVID-19 Deaths by Sex and Age (138,000+ observations)
- **Secondary**: U.S. Census Bureau state population estimates (2020-2023)
- **Time Period**: January 2020 - December 2023

## Methodology

### Exploratory Data Analysis
- **Data Cleaning**: Converted COVID-19 deaths from factor to numeric, handled CDC-suppressed values (39,430 NAs preserved to avoid bias)
- **Missing Value Analysis**: Systematic examination of NA patterns across key variables
- **Temporal Trends**: Time series visualization of monthly deaths (2020-2023) with smoothed curves
- **Seasonal Patterns**: Boxplot analysis revealing highest mortality in December/January, lowest in June
- **Age Distribution**: Pie chart analysis of total deaths by age group
- **Geographic Visualization**: Choropleth heatmaps showing state-level mortality rates per 100,000 across four years
- **Data Integration**: Left join of CDC mortality data with U.S. Census population estimates for rate standardization

### Statistical Models
- **Multiple Linear Regression**: Baseline model (R² = 0.636)
- **Poisson Regression**: Count-based modeling with population offset
- **Negative Binomial Regression**: Best-fit model (AIC = 44,747) accounting for overdispersion
- **Two-Way ANOVA**: Interaction effects between age, sex, and region

### Key Techniques
- Population-standardized mortality rates (per 100,000)
- Geographic heatmap visualization with region classification (West, Midwest, South, Northeast)
- Panel data analysis by region
- Interaction plots for age-sex effects
- Diagnostic residual analysis (Q-Q plots, residuals vs fitted)

## Technologies Used

**Language**: R  
**Document Generation**: Quarto  
**Key Packages**: 
- Data manipulation: `tidyverse`, `dplyr`
- Visualization: `ggplot2`, `maps`, `patchwork`
- Modeling: `MASS` (Negative Binomial), base R (GLM, ANOVA)
- Tables: `flextable`, `modelsummary`, `kableExtra`
- Date handling: `lubridate`


## Getting Started

### Prerequisites
- R (version 4.0+)
- Quarto
- Required R packages (see Methods section)

### Running the Analysis

**Generate the PDF report:**

Run `report/report.qmd`


**View complete workflow:**

Run `src/main.qmd`


## Results Summary

Our Negative Binomial regression model provided the best fit for the overdispersed count data, demonstrating that demographic and geographic factors played significant roles in shaping COVID-19 mortality patterns. The analysis revealed substantial variation across states and time periods, with clear age-stratified risk profiles and regional disparities that have important implications for public health policy and future pandemic preparedness.

## Limitations

- CDC data suppression in low-population subgroups resulted in 39,430 missing values
- Observations are not statistically independent (spatial and temporal correlation)
- Large sample size leads to statistical significance for small effect sizes
- Does not include vaccination coverage, variant data, or county-level granularity

## Future Directions

- Incorporate vaccination rates and variant prevalence data
- Implement time series models (GAM, ARIMA) for seasonal pattern analysis
- Expand to county-level analysis for finer geographic resolution
- Apply mixed-effects models to account for hierarchical data structure

## Authors

Dane Pearson & Dhriti Avala

December 9, 2025

## References

Data sourced from CDC National Center for Health Statistics and U.S. Census Bureau. Full citations available in the report.

---

*This project was completed as part of STAT 204: Statistical Data Analysis at the University of California, Santa Cruz*

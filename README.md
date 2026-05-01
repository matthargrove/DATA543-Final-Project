# DATA543-Final-Project

# Clean Energy Construction Delay Risk & Tax Credit Pricing

This repo contains code, data, and machine learning models used to analyze construction delays in U.S. clean energy projects and their impact on transferable tax credit pricing. The project focuses on solar and wind infrastructure from 2023 to 2025, with an emphasis on financial risk exposure and insurance implications.

***

**Abstract**
>The Inflation Reduction Act (IRA) introduced transferability for clean energy tax credits, creating a rapidly growing secondary market. However, recent policy changes, specifically the One Big Beautiful Bill Act (2025), which have imposed strict deadlines on eligibility, makes construction delays a critical financial risk. This study investigates whether construction delays are predictable and whether delay risk is reflected in tax credit pricing. Using project-level data from EIA Form 860M and pricing data from Crux Climate, we apply machine learning and statistical methods to analyze delay behavior and its financial implications. Our results indicate that construction delays are highly stochastic and difficult to predict using standard project features. While machine learning models show moderate predictive power in some cases, delays are largely driven by localized, idiosyncratic factors. From a financial perspective, this creates significant exposure for tax credit investors, particularly in solar projects where eligibility is binary. The findings highlight the importance of specialized risk management tools, including insurance based approaches.

***

## Introduction & Background

The Inflation Reduction Act of 2022 introduced transferability for clean energy tax credits, allowing developers to sell Investment Tax Credits (ITCs) and Production Tax Credits (PTCs) to unrelated buyers for cash. This created a secondary market where credits trade at a discount to face value, typically **89 to 95 cents per dollar**. The market grew rapidly, with transfer volume reaching approximately **$42 billion in 2025**, up 27% from the prior year.

However, the **One Big Beautiful Bill Act (OBBBA)**, signed July 4, 2025, imposed sweeping restrictions. For wind and solar, the ITC and PTC will be eliminated for projects placed in service after December 31, 2027, unless construction begins by July 4, 2026. These compressed timelines transformed construction delays from a practical inconvenience into a financial risk. A project that falls behind schedule may miss its eligibility window entirely, rendering its credits worthless.

This project investigates whether construction delay risk is priced into the transferable tax credit market. We use project level data from the EIA (Form 860M) on solar and wind projects from 2023 to 2025, combined with quarterly pricing data from Crux Climate. Our framework applies machine learning to quantify the price discount associated with delays and identify the primary drivers of delay risk.

***

## Research Question & Motivation

This project addresses the following core question:

**Does construction delay risk reduce the market price of transferable tax credits, and what does this imply for insurance pricing?**

The motivation is driven by:
* A rapidly growing tax credit transfer market (~$42B in 2025)
* Policy imposed deadlines (**OBBBA**) that create **binary eligibility risk**
* Increasing price discounts in ITC and PTC markets
* High exposure in solar heavy development pipelines

The exposure is substantial. The near term pipeline is dominated by solar, with planned capacity peaking at **36 GW in 2026**, the largest build cycle in U.S. history. Solar projects claim ITCs, which are binary because the project either qualifies or it does not. Pricing data confirms growing vulnerability; ITC prices fell from 93.5 cents in Q4 2024 to 89 cents by Q4 2025. Approximately 45% of planned capacity falls into a "low confidence" category, where the probability of missing a tax deadline is meaningfully higher.

***

## Data Sources

The analysis integrates two primary datasets:

### 1. [EIA Form 860M](https://www.eia.gov/electricity/data/eia860m/) (2022 to 2025)
* **PLANNED data:** Proposed and under construction projects (December data used).
* **OPERATING data:** Completed projects with actual operation dates.
* Used to track project transitions and calculate delays for 1,044 completed projects.

### 2. [Crux Climate](https://www.cruxclimate.com/) Pricing Data
* Quarterly ITC and PTC prices (2023 H2 to 2025 Q4).
* Expressed in cents per dollar of credit face value.
* Used to analyze market response to risk.

The dataset focuses exclusively on:
* Solar Photovoltaic and Thermal
* Onshore and Offshore Wind

***

## Data Processing & Feature Engineering

Key preprocessing steps include:

* **Filtering:** Isolated solar and wind energy projects from all years.
* **Unique Identification:** Created a unique `plant_key` by concatenating 'Entity ID' and 'Plant ID' to ensure consistent tracking across different annual datasets.
* **Project Matching:** Merged PLANNED data with OPERATING data (2023 to 2025) using the `plant_key` and 'Technology' to identify completed projects.
* **Delay Calculation:** 
  * `delay_months` = Actual Operating Date − Planned Operation Date
* **Quarterly Aggregation:** Grouped project data by 'Actual Operating Year Quarter' to calculate total capacity (MW), average delay, and number of projects.
* **Price Integration:** Merged aggregated quarterly delays with Crux Climate price data, aligning periods such as mapping 2023Q3 and 2023Q4 to '2023H2'.

***

## Methodology

### Regression Analysis
* Simple linear regression used to investigate the statistical relationship between `average_delay_months` and ITC or PTC prices using the merged quarterly data.

### Machine Learning Model
* **Model:** Random Forest Regressor (chosen for robustness and handling non linear relationships).
* **Target:** `delay_months`
* **Features:** Nameplate Capacity (MW), Planned Operation Month and Year, Technology, Status (project stage), Latitude, and Longitude.

Pipeline includes:
* One hot encoding for categorical variables ('Technology', 'Status').
* Train and test split (80/20).
* Evaluation metrics: MAE, RMSE, and $R^2$ Score.

### Additional Analysis
* **Technology Specific Modeling:** Independent pipelines for Solar Photovoltaic and Onshore Wind to assess accuracy at a granular level.
* **Tail Risk Estimation:** Analysis of 95th percentile delays and financial exposure.

***

## Machine Learning Concepts Used

* **Ridge Regression**: Chosen for its efficiency in predicting continuous outcomes with L2 regularization.
* **Feature Engineering:** Creating delay metrics, temporal features, and spatial coordinates.
* **Preprocessing:** One hot encoding and scaling via a structured scikit-learn pipeline.
* **Model Evaluation:** Assessment via MAE (Mean Absolute Error), RMSE (Root Mean Squared Error), and $R^2$ (Coefficient of Determination).
* **Risk Analysis:** Tail risk estimation and binary eligibility loss calculations.

***

## Results & Key Findings

### 1. Delay Predictability
* **Overall Model Performance:**
  * **MAE:** 1.71 months
  * **RMSE:** 2.93 months
  * **$R^2$:** 0.63 (Indicating selected characteristics are moderately effective).

* **Stochastic Nature of Delays:**
  * Further analysis using Ridge Regression yielded a low **$R^2$ (~0.0468)**, suggesting that construction delays are highly stochastic and idiosyncratic.
  * This indicates that delays are driven by complex, local factors rather than simply geography or project size, reinforcing the need for specialized risk management over generalized forecasting.

* **Technology Disparity:**
  * **Solar Photovoltaic ($N=970$):** Strong performance (**$R^2 \approx 0.70$**). Large sample size and consistent development characteristics allow for more reliable prediction.
  * **Onshore Wind Turbine ($N=74$):** Weak performance (**$R^2 \approx 0.22$**). Errors exceed six months, likely due to smaller sample size and more volatile local factors.

***

### 2. Relationship to Tax Credit Prices
* **Lack of Statistical Significance:** P-values for ITC (0.267) and PTC (0.491) exceed 0.05.
* **Low Explanatory Power:** **$R^2 < 0.30$** for price models.
* **Conclusion:** Delay risk may not yet be fully priced into the market as a standalone variable, or the limited quarterly observations ($N=6$) lack the power to detect the relationship.

***

### 3. Financial Risk & Insurance Insights
* **The Q4 Trap:** Projects planned for the final quarter have a **41% to 46% probability** of slipping into the next tax year. This potentially jeopardizes immediate tax credit eligibility and negatively impacts project Internal Rates of Return (IRR).
* **Binary Exposure:** Solar projects show higher financial risk density due to ITC structures where eligibility is binary.
* **Estimated Losses:** Potential revenue losses exceed **$1.5 billion** across the pipeline.
* **Tail Risk & Insurance:** The 95th percentile "tail risk" is approximately **18 months**. While average delays are moderate, this extreme outlier value provides a concrete baseline for pricing Delay in Start Up (DSU) insurance premiums.
* **Loss Standardization:** By standardizing losses per MW, the analysis offers a transparent framework for comparing risk across technologies, specifically highlighting the unique volatility associated with solar and onshore wind infrastructure.

***

## Installation and Setup
* We used Colab, so download all of the .ipynb files and load them into Google Colab. From there, upload the .csv files into the content folder and begin running code.

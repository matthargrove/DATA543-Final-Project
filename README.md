# DATA543-Final-Project

# Clean Energy Construction Delay Risk & Tax Credit Pricing

This repository contains code, data processing pipelines, and machine learning models used to analyze construction delays in U.S. clean energy projects and their impact on transferable tax credit pricing. The project focuses on solar and wind infrastructure from 2023–2025, with an emphasis on financial risk exposure and insurance implications.

---

**Abstract**
>The Inflation Reduction Act (IRA) introduced transferability for clean energy tax credits, creating a rapidly growing secondary market. However, recent policy changes, specifically the One Big Beautiful Bill Act (2025), which have imposed strict deadlines on eligibility, makes construction delays a critical financial risk. This study investigates whether construction delays are predictable and whether delay risk is reflected in tax credit pricing. Using project-level data from EIA Form 860M and pricing data from Crux Climate, we apply machine learning and statistical methods to analyze delay behavior and its financial implications. Our results indicate that construction delays are highly stochastic and difficult to predict using standard project features. While machine learning models show moderate predictive power in some cases, delays are largely driven by localized, idiosyncratic factors. From a financial perspective, this creates significant exposure for tax credit investors, particularly in solar projects where eligibility is binary. The findings highlight the importance of specialized risk management tools, including insurance-based approaches.

---

## Research Question & Motivation

This project addresses the following core question:

**Does construction delay risk reduce the market price of transferable tax credits, and what does this imply for insurance pricing?**

The motivation is driven by:
- A rapidly growing tax credit transfer market (~$42B in 2025)
- Policy-imposed deadlines that create **binary eligibility risk**
- Increasing price discounts in ITC/PTC markets
- High exposure in solar-heavy development pipelines

Construction delays can directly impact:
- Tax credit eligibility
- Investor returns (IRR)
- Market pricing dynamics
- Insurance demand (e.g., Delay-in-Start-Up insurance)

---

## Data Sources

The analysis integrates two primary datasets:

### 1. EIA Form 860M (2022–2025)
- **PLANNED data:** Proposed and under-construction projects
- **OPERATING data:** Completed projects with actual operation dates
- Used to track project transitions and calculate delays

### 2. Crux Climate Pricing Data
- Quarterly ITC and PTC prices (2023–2025)
- Expressed in cents per dollar of face value
- Used to analyze market response to risk

The dataset focuses exclusively on:
- Solar Photovoltaic and Thermal
- Onshore and Offshore Wind

---

## Data Processing & Feature Engineering

Key preprocessing steps include:

- Filtering for solar and wind projects
- Creating a unique `plant_key` for tracking projects across datasets
- Matching planned projects to completed projects
- Calculating:
  - `delay_months` = Actual Date − Planned Date
- Aggregating data quarterly:
  - Total capacity (MW)
  - Average delay
  - Number of projects
- Merging with tax credit price data

Engineered features include:
- Planned year and month
- Project status
- Geographic coordinates
- Technology type
- Capacity (MW)

---

## Methodology

### Regression Analysis
- Linear regression used to test relationship between:
  - Delays and ITC prices
  - Delays and PTC prices

### Machine Learning Model
- **Model:** Random Forest Regressor  
- **Target:** `delay_months`  
- **Features:** Capacity, time variables, technology, status, geography  

Pipeline includes:
- One-hot encoding for categorical variables
- Train/test split (80/20)
- Evaluation metrics:
  - MAE
  - RMSE
  - R² Score

### Additional Analysis
- Technology-specific modeling (solar vs wind)
- Tail risk estimation (95th percentile delays)
- Financial exposure calculations

---

## Machine Learning Concepts Used

- **Ridge Regression:** A regression type used for predicting continuous outcomes (delays and capacity) using L2 regularization.
- **Feature Engineering:** Creating delay metrics and temporal features  
- **Preprocessing:** Scaling and encoding features  
- **Model Evaluation:** MAE, RMSE, and R²  
- **Model Interpretability:** Feature importance analysis  
- **Pipeline Design:** End-to-end sklearn workflow  
- **Risk Analysis:** Tail risk and loss estimation  

The implementation was inspired by a sklearn-based regression demo, incorporating structured pipelines and feature transformations. 

---

## Results & Key Findings

### 1. Delay Predictability
- Overall model performance:
  - **MAE:** ~1.71 months  
  - **RMSE:** ~2.93 months  
  - **R²:** ~0.63  

- Solar projects:
  - Strong predictive performance (R² ≈ 0.70)

- Wind projects:
  - Weak performance (R² ≈ 0.22)

 **Conclusion:** Delays are partially predictable but still heavily driven by unpredictable, local factors.

---

### 2. Relationship to Tax Credit Prices
- No statistically significant relationship found between delays and ITC/PTC prices
- Low explanatory power (R² < 0.30)
- Limited sample size (N=6 quarters) reduces statistical strength

 **Conclusion:** Delay risk may not yet be fully priced into the market or requires more granular data.

---

### 3. Financial Risk Insights
- Solar projects show **higher financial exposure**
- Estimated losses exceed **$1.5 billion**
- Identified **“Q4 Trap”**:
  - 41–46% probability of slipping into the next tax year
- 95th percentile delay ≈ **18 months**

 **Conclusion:** Extreme delay scenarios create meaningful downside risk for investors and insurers.

---


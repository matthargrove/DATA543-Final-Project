# DATA543-Final-Project

# Clean Energy Construction Delay Risk & Tax Credit Pricing

This repo contains code, data, and machine learning models used to analyze construction delays in U.S. clean energy projects and their impact on transferable tax credit pricing. The project focuses on solar and wind infrastructure from 2023 to 2025, with an emphasis on financial risk exposure and insurance implications.

***

## 1.Introduction & Background

Clean energy tax credits come in two forms: Investment Tax Credits (ITCs), which are one-time credits based on project cost and primarily used by solar projects, and Production Tax Credits (PTCs), which are ongoing payments based on electricity generated and mainly used by wind projects. The Inflation Reduction Act of 2022 allowed developers to sell these credits to unrelated buyers for cash, creating a secondary market that reached $42 billion by 2025 with credits trading at 89–95 cents per dollar. The policy landscape shifted dramatically when the One Big Beautiful Bill Act (OBBBA), signed July 4, 2025, eliminated ITC/PTC eligibility for wind and solar projects placed in service after December 31, 2027, unless construction began by July 4, 2026. This compressed timeline means construction delays can now render credits entirely worthless. Our study asks: does this delay risk reduce market prices for transferable credits, and what do delay patterns tell us about pricing insurance against this risk?



## 2.Methdology 
## 2.1 Data Sources and Integration
We combined two datasets. The first is EIA Form 860M (https://www.eia.gov/electricity/data/eia860m/), which provides monthly generator-level data on every U.S. power plant. We used four annual snapshots (January 2023–2026), filtering for solar and wind projects, to track when projects were planned to operate versus when they actually came online. The second is Crux Climate https://www.cruxclimate.com/), which reports quarterly ITC and PTC market prices from 2023 H2 through 2025 Q4. We converted EIA monthly data to quarterly frequency to match Crux’s reporting cadence, and defined construction delay as the difference between a project’s actual operating date and its first planned date, measured in months.


## 2.2 Analytical Approach
We first used a ridge regression to identify which project features (technology type, location, capacity) drive construction delays. Ridge regression works like a standard regression but includes a penalty that prevents any single variable from overwhelming the model, which is helpful when features are correlated. From the resulting delay predictions, we built a risk score for each project (delay months × MW capacity) and aggregated these by region to map where financial exposure is concentrated.

After that, we then ran a simple linear regression to test the core question: do quarterly average delays statistically explain ITC/PTC price movements? Finally, we trained a random forest model to predict delays at the individual project level. A random forest builds many decision trees on different subsets of data and averages their predictions, capturing non-linear patterns that a single regression line would miss. This tests whether delay risk is predictable enough to be “insurable”—that is, whether you can price a policy around it.

***

## 3. Results & Key Findings
3.1 Drivers of Delay
The ridge regression showed that delay risk is driven primarily by technology and geography. Offshore wind had the largest positive effect on predicted delay, reflecting the logistical complexity of marine construction. Latitude was the second strongest predictor, pointing to regional differences in permitting and grid infrastructure. Onshore wind actually reduced predicted delay relative to the baseline, while solar and project capacity had moderate effects.

3.2 Geographic Risk Concentration
Risk scores are heavily concentrated in the South (driven by the large Texas and Southeast solar pipeline), followed by the Midwest and West. The Northeast carries relatively less exposure. This clustering matters because projects in the same region share permitting authorities and interconnection queues, so a single bottleneck can create cascading delays

3.3 Timing and Tail Risk
Projects scheduled to come online in the last quarter of the year (months 9–12) face roughly a 40%+ probability of slipping into the next tax year—compared to just 5–8% for early-year projects. Under OBBBA’s rules, that slip means total credit loss. The delay distribution also has a long tail: the 95th percentile delay is approximately 18 months, meaning 5% of projects face delays of a year and a half or more. These extreme cases drive the most significant financial and insurance risk.

3.4 Financial Impact
We estimated total delay-related losses of approximately $547 million for solar, $132 million for onshore wind, and $15 million for offshore wind. Solar dominates not because individual solar projects are the riskiest, but because the pipeline is so much larger. Offshore wind carries the highest per-project risk but has far fewer active projects.


3.5 Do Delays Affect Market Prices?
Our linear regression found no statistically significant relationship between average construction delays and quarterly credit prices. The ITC regression returned a p-value of 0.267 (R² = 0.293) and PTC returned a p-value of 0.491 (R² = 0.125)—both well above the 0.05 significance threshold. However, this null result should be read cautiously: we only had six quarterly observations, which is too few to detect all but the strongest effects. Other factors like policy sentiment and buyer demand likely dominate at the aggregate level, and the relationship may exist at the project level but get averaged away in quarterly data.

3.6 Predicting Individual Project Delays
The random forest model delivered the study’s most actionable result. Overall, it achieved a mean absolute error of 1.71 months and an R² of 0.63. Broken down by technology, solar projects (N=970) showed strong predictability with an R² of 0.70 and MAE of 1.91 months, while wind projects (N=74) were much harder to predict at R² = 0.22 and MAE of 6.13 months. In other words, solar delays are predictable enough to support standardized insurance products with actuarially sound premiums, while wind delays require bespoke, case-by-case underwriting.

## 4. Conclusion and Future Work

We did not find statistical evidence that aggregate delay risk currently drives market prices, though this is likely a data limitation rather than proof of irrelevance. On the prediction side, solar delays are highly predictable (R² = 0.70), supporting standardized insurance pricing, while wind remains too uncertain (R² = 0.22) for the same approach. Two actuarial benchmarks stand out: the 18-month 95th-percentile tail risk and the 41–46% Q4 slippage probability, both of which provide objective bounds for risk-adjusted premiums.

Future work should focus on expanding the pricing sample as the market matures, securing transaction-level data for project-level price analysis, and identifying additional features to improve wind delay predictability beyond its current 22%.


***

## Installation and Setup
* We used Colab, so download all of the .ipynb files and load them into Google Colab. From there, upload the .csv files into the content folder and begin running code.

# UNICEF_project

This project develops and evaluates propensity models that estimate the likelihood of one-time donors switching to long-term commitments. The aim is to improve efficiency at UNICEF so that more resources can be allocated to children in need. 

The likelihood is estimated by analyzing historical donation patterns and donor demographics. The optimized model achieves a 5x lift over the random baseline and performs twice as well as UNICEF's current approach. Based on the result, the team identified the most desirable group by age and income level, and recommended an early, fast contact strategy to maximize conversion rates. Through cost-benefit analysis, the team estimated that UNICEF could save around $100,000 annually, or 0.3-1% approximately, on the conversion expenses by adopting the project's proposal. 


## Data Source

The primary dataset was provided by UNICEF (confidential and not publicly available). To supplement this, we incorporated Australian Bureau of Statistics (ABS) and Mosaic datasets to analyze income levels by geographical location. These external datasets enabled a coarse-grained demographic analysis, improving model accuracy while maintaining privacy by avoiding the use of individual-level sensitive data.


## Methods/ Approach: 

### Data Handling & Exploratory Analysis

We first addressed potential data issues to ensure reliability before modeling:  
- Checked for missing values, inconsistencies, and outliers.
- Corrected data errors based on updated instructions, research, and rules.  
- Visualized key patterns using bar charts, box plots, and scatter plots.
- Tested significance of the relationships using chi-square tests, t-tests, point-biserial correlations, and other statistical methods.


### Key Findings
- **Campaign contact bias:** UNICEF’s data lacked clear information on how many times donors were approached for Regular Giver (RG) conversion. To address this, we identified campaigns most likely designed for RG acquisition and excluded donors unlikely to have been contacted. A chi-square test confirmed statistical significance (*p* < 0.001).  
- **Donor demographics:** Donors aged **20–40** and living in **high-income suburbs** showed the highest conversion likelihood.
- **Availability indicator:** The likelihood of conversion follows an inverted U-shaped pattern with respect to the number of contact methods provided.
- **Status quo bias:**  The donation channel through which donors make their first contribution has a statistically significant effect on their likelihood of conversion.
- **Recency effect:** Donors were most likely to convert shortly after their most recent donation, with the optimal window around 30 days, and this effect weakens over time.  

⚠️ **Note on data leakage:** Recency was included as a predictor, but since it requires assumptions about time snapshots, it is interpreted cautiously as a strategic indicator rather than a pure predictive feature.  









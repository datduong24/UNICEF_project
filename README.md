# UNICEF project

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


#### Key Findings
- **Campaign contact bias:** UNICEF’s data lacked clear information on how many times donors were approached for Regular Giver (RG) conversion. To address this, we identified campaigns most likely designed for RG acquisition and excluded donors unlikely to have been contacted. A chi-square test confirmed statistical significance (*p* < 0.001).  
- **Donor demographics:** Donors aged **20–40** and living in **high-income suburbs** showed the highest conversion likelihood.
- **Availability indicator:** The likelihood of conversion follows an inverted U-shaped pattern with respect to the number of contact methods provided.
- **Status quo bias:**  The donation channel through which donors make their first contribution has a statistically significant effect on their likelihood of conversion.
- **Recency effect:** Donors were most likely to convert shortly after their most recent donation, with the optimal window around 30 days, and this effect weakens over time.  

⚠️ **Note on data leakage:** Recency was included as a predictor, but since it requires assumptions about time snapshots, it is interpreted cautiously as a strategic indicator rather than a pure predictive feature.  


### Feature Engineering

To capture key behavioral and demographic factors, we engineered several new features and refined existing ones:  

- **Donor segmentation:** Separated single-gift (SG), recurring-gift (RG), and converters to study behavior patterns leading up to conversion. Found that most conversions occur within 3 months of consistent donations, while late converters (~3 years) are rare outliers (<4%).  
- **Demographic profiling:** Used ABS and Mosaic data to classify suburbs by income and age groups (high, medium, low income; young, middle-aged, elderly; households with/without children). Each suburb was represented as a mix of household types, capturing both dominant and non-dominant influences on donation behavior.  
- **Postcode encoding:** Applied K-fold target encoding by postcode to reduce bias and approximate cultural factors influencing donor behavior.  
- **RFM features:** Created Recency, Frequency, and Monetary variables within a 30-day snapshot, confirming that donors with consistent, moderate giving patterns are more likely to convert than those with sporadic large gifts.  
- **Contactability:** Engineered a variable combining preferred and available contact methods, revealing an inverted U-shaped relationship between number of channels and conversion likelihood.  

These engineered features significantly improved model accuracy while aligning variables with real-world donor engagement patterns.  


### Model Selection

We evaluated several classification models to predict donor conversion, optimizing them using PR AUC metric:  
- **Decision Tree** – baseline model, tuned using cross-validation.  
- **Random Forest** – trained with out-of-bag and boostrapping sampling to reduce computation time and improve generalization.  
- **XGBoost** – leveraged GPU acceleration (RAPIDS cuDF) for efficient training; tuned with random search cross-validation.  
- **Neural Network (TensorFlow)** – built with 4–6 hidden layers, ReLU activation, and early stopping to prevent overfitting.  

#### Handling Class Imbalance
Since only a small proportion of donors convert, we balanced classes by:  
- Using the `class_weight="balanced"` option where available.  
- Applying sample reweighting for models without built-in class balancing.

### Evaluation

Models were compared using **Average F1 score** as the primary metric, given the class imbalance. ROC AUC and PR AUC were also reported for robustness.  

- **Decision Tree** – offered high interpretability but had the weakest performance, with an F1-score of 0.58.
- **Random Forest** – achieved strong predictive power with PR AUC of 0.182 and F1 score of 0.605, but required longer training times.  
- **XGBoost** – delivered the best overall performance, achieving a **5x lift over the random baseline**  with PR AUC of 0.188 and performing twice as well as UNICEF’s current approach. The model achieved the F1 score of 0.608
- **Neural Network** – underperformed compared to XGboost and Random forest with F1 score of 0.6, and computationally intensive.
  
While all models achieved similar F1-scores (~0.6), XGBoost provided the largest PR AUC (0.188) and a 5× lift over random baseline, making it the most effective approach.

### Key Business Insights:
- **Model performance**: XGBoost achieved the strongest results, delivering a 5× lift over the random baseline and outperforming UNICEF’s current approach by nearly 2×.
- **Demographic targeting**: Donors aged 20–40 from high-income suburbs had the highest conversion likelihood.  
- **Recency effect**: Donors were most likely to convert shortly after their most recent gift, where the best window is 30 days after most recent gift  .  
- **Financial impact**: Adopting the proposed strategy could save **~$100,000 annually (0.3–1% of conversion expenses)**.  

These findings not only validate the model but also provide actionable strategies for improving UNICEF’s fundraising efficiency.  

### Strategic recommendations: 
- Adopt an early engagement strategy, with personalised nudges within 30 days of the last donation.
- Use multi-channel outreach (SMS, email, direct mail) tailored to donor preferences.
- Replace generic campaigns with data-driven, personalised flows, improving both conversion efficiency and donor experience

### Limitations & Assumptions

- **Data quality and coverage**  
  - The dataset contained gaps and missing values, especially in demographic attributes.  
  - Proxy variables (e.g., postcode as a socioeconomic indicator) may not fully capture donor intent or loyalty.  
  - Qualitative factors such as donor satisfaction or engagement sentiment were not available.  

- **Model assumptions**  
  - Insights were derived from historical data, which may not fully represent future donor behaviour.  
  - Training data was skewed towards high-frequency donors, limiting generalisability across the full donor base.  
  - Donor recency, channel preference, and postcode score were strong predictors, but may shift over time.  

- **Financial projections**  
  - Revenue impact was estimated assuming 12 months of continuous donor retention after conversion.  
  - Attrition rates may vary, leading to potential overestimation of projected revenue.  
  - Campaign cost estimates were based on typical public benchmarks and may differ from UNICEF’s actual operational costs.  

Overall, these limitations highlight the need for broader data collection (e.g., age, gender, engagement history, online interactions) and ongoing model retraining to improve predictive accuracy and ensure results remain actionable in practice.

### Tools & Libraries

- **Data Handling & Analysis**
  - Pandas  
  - NumPy
  - cuDF (GPU-accelerated dataframe library) 

- **Visualization**
  - Matplotlib  
  - Seaborn  

- **Statistical Analysis**
  - SciPy (stats, point-biserial correlation, t-tests, chi-square tests, Mann–Whitney U test)  
  - Statsmodels  

- **Machine Learning**
  - Scikit-learn (Logistic Regression, Decision Tree, Random Forest, KMeans, model selection, cross-validation, evaluation metrics)  
  - XGBoost
  - TensorFlow (Neural network)

- **Utilities**
  - Math 
  - Warnings (suppressing unnecessary outputs)  
  - Datetime (time-based features)  
  - Random & String (sampling and helper functions)
 
### Authors
- **Dat Duong** – Data preprocessing, visualization, feature engineering, model development, evaluation, reporting  
- **Mai Mai** – Data preprocessing, business analysis, reporting  
- **Sophia Nguyen** – Cost-benefit analysis, strategic recommendation, reporting  
- **Chloe Nguyen** – Data preprocessing, strategic recommendation, reporting  
- **Merry Trinh** – Marketing recommendation, reporting, cost analysis  


### Environment & Setup

> This project requires **Linux (Ubuntu 20.04/22.04+)** and an **NVIDIA GPU** with compatible CUDA drivers to run **RAPIDS/cuDF**.  

#### Prerequisites
- NVIDIA GPU + recent NVIDIA driver
- Conda/mamba (recommended: **Miniconda**)
- CUDA version compatible with your target RAPIDS release (see RAPIDS compatibility matrix)

#### Create the environment (GPU + RAPIDS)
```bash
# 0) Clone the repository
git clone https://github.com/your-username/unicef_project.git
cd unicef_project

# 1) Install Miniconda if you don't have it 
# 2) Create and activate an environment
conda create -n unicef-rapids python=3.10 -y
conda activate unicef-rapids

# 3) Install RAPIDS (this pulls cuDF and friends)
# Replace the placeholders with versions that match your driver:
#   <RAPIDS_VERSION> e.g., 24.06
#   <CUDA_VERSION>   e.g., 12.0 or 11.8
conda install -y -c rapidsai -c conda-forge -c nvidia \
  rapids=<RAPIDS_VERSION> cuda-version=<CUDA_VERSION>

# 4) Install the rest of the Python deps (cuDF is installed via conda above)
pip install -r requirements.txt

# 5) Run the jupyter notebook
jupyter notebook Assignment_group_3600-1.ipynb



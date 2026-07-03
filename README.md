# AI-Powered Recruitment Fraud Analytics & Risk Scoring

A fraud detection pipeline that flags fake job postings and scores them into risk tiers, with SHAP-based explainability so the "why" behind every flag is visible — not just a black-box probability.

Job scam postings cost applicants money, data, and time, and they're often hard to spot manually since scammers copy the format of real listings. This project builds a model that catches them anyway, using structural cues (missing salary, no company profile) more than the actual wording of the post.

# Pipeline
# 01_Data_Preparation
Loads the raw CSV, cleans salary ranges, and writes everything into a SQLite database (job_scam.db) as 6 relational tables. Fraud-rate-by-industry and fraud-rate-by-experience breakdowns are computed here with SQL.
# 02_Exploratory_Analysis_and_Feature_Engineering
Builds the 317-feature matrix used for training: 17 structured features (missing-field flags, red-flag keyword counts, text length stats, encoded categoricals) plus 300 TF-IDF features from posting text.
# 03_AI_Risk_Scoring_Model
Trains Random Forest and XGBoost, handles the class imbalance with scale_pos_weight, and tunes the classification threshold using F2 score instead of the default 0.5, since catching scams matters more than avoiding false alarms here.
# 04_Explainable_AI_and_Model_Interpretation
Runs SHAP to explain predictions globally and for individual postings, builds a Low/Medium/High risk scorecard, and exports the scored dataset for Power BI.


# Results

Model: XGBoost beat Random Forest on every metric.

ModelTest AUCCV AUCRandom Forest0.9680.962 ± 0.010XGBoost0.9810.978 ± 0.008

At the default 0.5 threshold, XGBoost catches 81% of scams. Since missing a scam is worse than a false alarm, I tuned the threshold to 0.273 (F2-optimal), which pushes scam recall to 92% at the cost of some precision (53%) — a deliberate tradeoff, not a limitation.

Risk tiers actually work — fraud rate increases cleanly as risk tier goes up, which is the real validation that the model ranks risk correctly rather than just classifying.

# Capstone_Masai_FinalProject_Kannan

Capstone Masai Final Project Source 
Project Title
=====
Movie Revenue Prediction and Explainable AI using Machine Learning and Large Language Models (LLM)

Project Objective

The objective of this project is to predict movie revenue using historical information from the TMDB Movies dataset. The project performs both regression (predicting revenue) and classification (predicting whether revenue is above or below the median), followed by integrating a Large Language Model (LLM) to generate structured, human-readable explanations for each prediction.

Part 1 – Data Cleaning and Exploratory Data Analysis (EDA)
Activities
Loaded and explored the TMDB Movies dataset.
Validated data types and handled missing values.
Applied median imputation for skewed features.
Detected and retained genuine outliers.
Performed descriptive statistics and skewness analysis.
Created histograms, scatter plots, box plots, and correlation heatmaps.
Compared Pearson and Spearman correlations.
Performed grouped aggregation by primary genre.
Generated the cleaned dataset for machine learning.
Output
Cleaned and analysis-ready dataset.
Exported cleaned_data.csv.
Part 2 – Regression and Classification Models
Regression

Models:

Linear Regression
Ridge Regression

Evaluation Metrics:

| Model             |             MSE |   R² Score |
| ----------------- | --------------: | ---------: |
| Linear Regression | **7.48 × 10¹⁵** | **0.5035** |
| Ridge Regression  | **7.54 × 10¹⁵** | **0.5000** |


Selected Model: Linear Regression
====
Reason
==
Lower MSE
Higher R²
Better overall regression performance
Classification

Model:

Logistic Regression

Evaluation Metrics:

Metric	Value
| Metric    |      Value |
| --------- | ---------: |
| Accuracy  | **77.91%** |
| Precision |    **85%** |
| Recall    |    **61%** |
| F1-score  |    **71%** |
| ROC-AUC   | **0.8356** |


Threshold Optimization:
=

Best Threshold: 0.35

Regularization: Best Logistic Regression:

C = 1.0

Bootstrap Analysis:
==

Mean AUC Difference: 0.0082

95% Confidence Interval: [0.0053, 0.0115]

Part 3 – Advanced Machine Learning Models
==
Decision Tree
=

Baseline:
=

| Training | Testing |
| -------: | ------: |
|   99.94% |  71.56% |


Result: Overfitting

Controlled Decision Tree
==

| Training | Testing |
| -------: | ------: |
|   81.39% |  79.89% |


Result: Better Generalization

Random Forest
====
Performance:
===
Metric	Value
===
| Metric            |      Value |
| ----------------- | ---------: |
| Training Accuracy | **82.87%** |
| Test Accuracy     | **79.89%** |
| ROC-AUC           | **0.8703** |


Top Features:
====

Budget
Runtime
Release Year
Release Month
Adventure


Gradient Boosting
===
Performance:
======
Metric	Value
===
| Metric            |      Value |
| ----------------- | ---------: |
| Training Accuracy | **81.81%** |
| Test Accuracy     | **80.12%** |
| ROC-AUC           | **0.8732** |


Best Classification Model
=====
Cross Validation
===
5-Fold Stratified Cross Validation

| Model                 | Mean ROC-AUC |
| --------------------- | -----------: |
| Logistic Regression   |       0.8408 |
| Decision Tree         |       0.8587 |
| Random Forest         |       0.8705 |
| **Gradient Boosting** |   **0.8752** |


GridSearchCV
==
Best Parameters
==
n_estimators = 200

max_depth = None

min_samples_leaf = 5

Best Cross Validated ROC-AUC: 0.8708

Learning Curve

Training AUC: 0.9289 → 0.9306

Testing AUC: 0.8590 → 0.8696

Conclusion:
=====
The model continues to benefit from additional training data.

Model Serialization
====
Saved: best_model.pkl  using Joblib.

Successfully reloaded and predicted new movie records.

Part 4 – Explainable AI using LLM
=
API Integration
Secure API configuration
Reusable call_llm() function
HTTP POST communication

Prompt Engineering
=====
Designed a Zero-Shot Prompt.

Temperature Comparison:
=====
| Temperature | Behaviour     |
| ----------- | ------------- |
| 0           | Deterministic |
| 0.7         | More creative |


Selected: Temperature = 0

Structured Output

The LLM returned

{
 "prediction_label":"",
 "confidence_level":"",
 "top_reason":"",
 "second_reason":"",
 "next_step":""
}

Every response passed

JSON parsing
JSON Schema Validation
PII Guardrail

Regex successfully blocked

Email addresses
Phone numbers

Allowed only safe movie feature inputs.

End-to-End Pipeline
=======
Movie Features
        │
        ▼
Random Forest Prediction
        │
        ▼
Prediction Probability
        │
        ▼
Prompt Construction
        │
        ▼
PII Guardrail
        │
        ▼
LLM API
        │
        ▼
Structured JSON Explanation
        │
        ▼
Schema Validation
        │
        ▼
Final Output

Final Project Summary
======
This project presents an end-to-end Machine Learning and Explainable AI solution for predicting movie revenue using the TMDB Movies dataset. The workflow began with comprehensive data cleaning and exploratory data analysis, including missing value treatment, feature engineering, skewness analysis, correlation analysis, and visualization. A cleaned dataset was then prepared for predictive modeling.

For the regression task, Linear Regression and Ridge Regression were developed to estimate movie revenue, with Linear Regression achieving the best performance (MSE = 7.48 × 10¹⁵, R² = 0.5035). For the classification task, Logistic Regression was trained to classify movies as above- or below-median revenue, achieving 77.91% accuracy and an ROC-AUC of 0.8356. Threshold optimization and regularization experiments further improved the understanding of model performance.

Advanced tree-based models were then explored, including Decision Tree, Random Forest, and Gradient Boosting. 
Among these, Gradient Boosting achieved the highest predictive performance with a test ROC-AUC of 0.8732 and the highest 5-fold cross-validated ROC-AUC of 0.8752, making it the recommended model for deployment. 
===
Hyperparameter tuning, feature ablation, learning curve analysis, and model serialization further strengthened the robustness and reproducibility of the solution.

Finally, the project integrated a Large Language Model (LLM) to generate structured explanations for machine learning predictions. A zero-shot prompt, JSON schema validation, regex-based PII guardrails, and deterministic prompt generation (temperature = 0) ensured that every prediction was accompanied by a secure, validated, and machine-readable explanation. The resulting system combines predictive analytics, model interpretability, and responsible AI practices, providing a complete end-to-end solution suitable for real-world decision-support applications.

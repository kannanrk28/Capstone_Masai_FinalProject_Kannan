
PART 2 - TASK 1 - Identify the features and label. Remove Noisy and Leaky feature
==================================================================================

| Feature                                                               | Reason                                                                                                                                                                                                   |
| --------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **revenue**                                                           | Target variable used for prediction.                                                                                                                                                                     |
| **revenue_adj**                                                       | **Data leakage** because it is directly derived from `revenue` through inflation adjustment.                                                                                                             |
| **id**, **imdb_id**                                                   | **Noisy features** because they are unique identifiers with no predictive value.                                                                                                                         |
| **homepage**                                                          | **Noisy feature** containing mostly unique URLs and many missing values.                                                                                                                                 |
| **overview**, **tagline**, **keywords**, **cast**, **original_title** | **Noisy features** because they are unstructured text or high-cardinality fields requiring advanced NLP preprocessing.                                                                                   |
| **popularity**, **vote_average**, **vote_count**                      | **Potential data leakage** because these values become available after a movie is released. If the objective is to predict revenue before release, they would not be available at prediction time.       |
| **budget_adj**                                                        | **Redundant feature** because it is an inflation-adjusted version of `budget` and is highly correlated with it, which may introduce multicollinearity without adding significant predictive information. |



Part 2 - Task 2 - Encoding Columns
==================================

Categorical Feature Encoding:
============================

The genres column was encoded using one-hot encoding because it contains nominal categories with no natural order, and a movie can belong to multiple genres. The genres were split using the | delimiter and converted into separate binary features. The director and production_companies columns were not encoded because they have very high cardinality (thousands of unique values), which would create a large number of sparse features, increase model complexity, and may lead to overfitting. Therefore, only genres was retained as the most informative categorical feature.

Label encoding was not used because assigning integer values to nominal categories would introduce a false ordinal relationship. For example, encoding Action = 0, Comedy = 1, and Drama = 2 incorrectly suggests that Drama is "greater than" Comedy or Action. One-hot encoding avoids this issue by representing each category as an independent binary feature without implying any ordering.

Label Encoding:
===============
No categorical columns in this dataset have a natural ordinal relationship, so label encoding was not applied.

Part 2 - Task 3 - Leak-Free Train-Test Split and Feature Scaling
================================================================

The dataset was split into 80% training and 20% testing sets using train_test_split() with random_state=42 to ensure reproducibility.

Feature scaling was performed using StandardScaler. The scaler was fitted only on the training data and then used to transform both the training and testing datasets.

This approach prevents data leakage because fitting the scaler on the entire dataset would use information from the test set (such as its mean and standard deviation) during preprocessing. As a result, the model would indirectly learn from the test data, leading to overly optimistic performance estimates. Fitting the scaler only on the training data ensures that the test set remains completely unseen until model evaluation.

Part 2 - Task 4 - Model Comparison
====================================

Heatmap co-releation
=====================
The correlation heatmap was used to identify relationships among the numerical features. The strongest correlation was observed between budget and budget_adj (absolute correlation ≈ 0.97), indicating strong multicollinearity because budget_adj is derived from budget. Most other feature pairs showed weak correlations (absolute correlation < 0.30), while a few genre combinations exhibited moderate correlations (0.30–0.70). Since budget and budget_adj provide nearly the same information, budget_adj was removed to reduce redundancy and improve model interpretability.

Model comparison
=================
Top 3 Features identified
=========================
The top three features were selected based on the largest absolute coefficient values. Although other features, such as Western, Release Year, and Animation, also have coefficients in the millions, their absolute values are smaller than those of Budget, Runtime, and History. Therefore, they have a comparatively smaller influence on the predicted revenue. This ranking reflects the relative contribution of each feature to the regression model rather than indicating that the remaining features are unimportant.

Regression Model – Linear Regression

A Linear Regression model was trained using the standardized training data to predict movie revenue. The model was evaluated using Mean Squared Error (MSE) and R² Score on the test dataset.

Model Performance
Model	MSE	R² Score
Linear Regression	7.48 × 10¹⁵	0.5035
Ridge Regression	7.54 × 10¹⁵	0.5000

The Linear Regression model achieved a slightly lower MSE and a slightly higher R² score than Ridge Regression, indicating marginally better predictive performance. Both models produced very similar results, suggesting that regularization had little impact on this dataset.

Top 3 Features Based on Absolute Coefficients
Feature	Coefficient	Interpretation
budget	+85,133,110	A one standard deviation increase in budget is associated with an increase of approximately $85.13 million in the predicted movie revenue, assuming all other features remain constant.
runtime	+4,744,721	A one standard deviation increase in runtime is associated with an increase of approximately $4.74 million in the predicted revenue.
History	−3,972,667	Movies belonging to the History genre are predicted to earn approximately $3.97 million less than movies that are not in this genre, assuming all other features remain constant.

A positive coefficient indicates that increasing the feature increases the predicted revenue, whereas a negative coefficient indicates that increasing the feature decreases the predicted revenue while keeping all other features constant.

Linear Regression vs Ridge Regression

Ridge Regression applies L2 regularization, which adds a penalty to large coefficient values during model training. This penalty helps reduce overfitting and stabilizes coefficients when features are highly correlated. The alpha parameter controls the strength of this penalty: a larger alpha results in stronger regularization and smaller coefficients, while a smaller alpha makes Ridge behave more like ordinary least squares (OLS) Linear Regression.

In this project, Ridge Regression produced performance very similar to Linear Regression because the final feature set had limited multicollinearity after removing redundant and noisy features. As a result, regularization provided little additional benefit, and Linear Regression achieved slightly better performance.

Why MSE is Large

The MSE value appears large because the target variable (movie revenue) is measured in dollars, and MSE squares the prediction errors. Therefore, MSE should be used primarily to compare models trained on the same dataset rather than being interpreted on its own.

Interpretation of R²

The R² score of approximately 0.50 indicates that the model explains about 50% of the variation in movie revenue using the selected features. The remaining variation is likely due to factors not included in the dataset, such as marketing budget, actor popularity, audience reviews, competition from other releases, and promotional activities.

Overall Conclusion

The Linear Regression model was selected as the preferred regression model because it achieved the lowest MSE and the highest R² score among the evaluated models. Its performance indicates a moderate ability to predict movie revenue, making it suitable as a baseline model for this dataset.

Part 2 - Task 5a - Classification Model – Logistic Regression
===========================================================

Logistic Regression Evaluation

The Logistic Regression model was evaluated using a confusion matrix, accuracy, precision, recall, F1-score, and ROC-AUC.

Model Performance
Metric	Value
Accuracy	77.91%
Precision	85%
Recall	61%
F1-score	71%
AUC	0.8356

(a) Precision and Recall Formulas
=================================
Precision
===========

Precision= Precision=TP/TP+FP
	​
TP (True Positives): Correctly predicted high-revenue movies.
FP (False Positives): Movies incorrectly predicted as high-revenue.

Precision measures how many predicted positive movies are actually positive.

Recall
======

Recall= TP+FN/TP
	​
TP (True Positives): Correctly predicted high-revenue movies.
FN (False Negatives): High-revenue movies incorrectly predicted as low-revenue.

Recall measures how many actual high-revenue movies are correctly identified by the model.

(b) Which Metric is More Important?
=====================================

For this project, Recall is considered more important because the objective is to identify movies that are likely to achieve above-median revenue. A False Negative means the model fails to identify a potentially successful movie, which could lead to missing valuable investment or business opportunities. Therefore, improving recall helps reduce the number of missed successful movies, even if it slightly increases the number of false positives.

(c) Interpretation of AUC
===========================

The model achieved an AUC score of 0.8356, indicating good classification performance.

An AUC of 0.8356 means there is approximately an 83.56% probability that the model will assign a higher prediction score to a randomly selected high-revenue movie than to a randomly selected low-revenue movie. This indicates that the model has a good ability to distinguish between the two revenue classes.

Part 2 - Task 5b - Classification Model – Logistic Regression - Decision Threshold Sensitivity
===========================================================================================


Decision Threshold Sensitivity

The Logistic Regression model was evaluated using different decision thresholds ranging from 0.30 to 0.70 to study the trade-off between Precision, Recall, and F1-score.

Threshold	Precision	Recall	F1-score
0.30	0.634	0.829	0.719
0.35	0.725	0.760	0.742
0.40	0.777	0.690	0.731
0.45	0.821	0.645	0.723
0.50	0.849	0.608	0.708
0.60	0.868	0.522	0.652
0.70	0.880	0.435	0.582
(a) Precision and Recall Formula

Precision
===========

Precision= Precision=TP/TP+FP
	​
TP (True Positives): Correctly predicted high-revenue movies.
FP (False Positives): Movies incorrectly predicted as high-revenue.

Precision measures how many predicted positive movies are actually positive.

Recall
======

Recall= TP+FN/TP
	​
TP (True Positives): Correctly predicted high-revenue movies.
FN (False Negatives): High-revenue movies incorrectly predicted as low-revenue.

Recall measures how many actual high-revenue movies are correctly identified by the model.

where:

TP = True Positives
FN = False Negatives

Recall measures the proportion of actual high-revenue movies that are correctly identified by the model.

(b) Best Threshold
===================

The highest F1-score (0.742) was obtained at a decision threshold of 0.35.

This threshold provides the best balance between Precision and Recall for this dataset.

(c) Which Metric is More Important?
====================================

For this project, Recall is more important because the objective is to identify movies that are likely to achieve above-median revenue. A False Negative means failing to identify a potentially successful movie, which could result in missed investment or business opportunities. Therefore, maximizing Recall helps reduce the number of missed successful movies.

(d) Should the Threshold be Raised or Lowered?
================================================

To improve Recall, the decision threshold should be lowered.

Reducing the threshold from 0.50 to 0.35 increased:

Recall from 60.8% to 76.0%
F1-score from 0.708 to 0.742

However, this comes at the cost of reducing Precision from 84.9% to 72.5%, meaning the model predicts more movies as high-revenue, which increases the number of False Positives.

Therefore, 0.35 was selected as the preferred decision threshold because it provides the best balance between identifying successful movies and maintaining acceptable prediction 
accuracy.

Part 2 - Task 6 - Regularization Experiment
=============================================
The C parameter in Logistic Regression controls the strength of L2 regularization. A larger value of C applies weaker regularization, allowing the model to fit the training data more closely, whereas a smaller value of C applies stronger regularization by shrinking the model coefficients toward zero, helping to reduce overfitting.

In this experiment, reducing C from 1.0 to 0.01 resulted in slightly higher precision (0.851 vs. 0.849), but recall decreased from 0.608 to 0.578, F1-score decreased from 0.708 to 0.688, and AUC decreased from 0.836 to 0.827. This indicates that stronger regularization caused the model to become slightly underfitted, reducing its ability to correctly identify high-revenue movies. Therefore, the baseline model (C = 1.0) achieved better overall performance and provides a better balance between precision, recall, and class discrimination for this dataset.

Part 2 - Task 7 -Bootstrap Confidence Interval for AUC Difference
==================================================================

A bootstrap analysis with 500 resamples was performed to compare the AUC of the baseline Logistic Regression model (C = 1.0) with the strongly regularized model (C = 0.01).

Mean AUC Difference: 0.0082
95% Confidence Interval: [0.0053, 0.0115]

The 95% confidence interval does not include zero, indicating that the baseline model (C = 1.0) consistently achieves a higher AUC than the strongly regularized model (C = 0.01) across different bootstrap samples. This suggests that the observed improvement is statistically reliable rather than due to random variation in the test data. Although the average improvement in AUC (0.0082) is relatively small, it consistently favors the baseline model, making C = 1.0 the preferred choice for this dataset.

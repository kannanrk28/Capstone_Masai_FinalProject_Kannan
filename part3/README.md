Decision Tree Baseline
========================
A baseline Decision Tree Classifier was trained using the default parameters (max_depth=None).

Model Performance
==================
Metric	Value
Training Accuracy	99.94%
Test Accuracy	71.56%
Overfitting Analysis

The Decision Tree model shows clear signs of overfitting. It achieved an almost perfect training accuracy of 99.94%, but its test accuracy dropped to 71.56%. This large gap indicates that the model learned the training data, including its noise and specific patterns, very well but did not generalize effectively to unseen test data.

Decision Trees are considered high-variance models because they build the tree by making the best (greedy) split at each node based on the available training data. Once a split is made, the algorithm does not revisit or revise earlier decisions. As a result, small changes in the training data can produce a significantly different tree structure. When no constraints such as max_depth or min_samples_leaf are applied, the tree can continue splitting until it almost memorizes the training data, which leads to overfitting and poorer performance on new, unseen data.

Conclusion
===========
The baseline Decision Tree achieved very high training accuracy but lower test accuracy, indicating that it memorized the training data rather than learning general patterns. To improve generalization, techniques such as tree pruning, limiting the maximum depth, or increasing the minimum number of samples required for a split or leaf node can be applied.

Controlled Decision Tree
========================
A second Decision Tree model was trained using max_depth = 5 and min_samples_split = 20 to reduce overfitting observed in the baseline Decision Tree.

Model Performance
Model	Training Accuracy	Test Accuracy
Baseline Decision Tree	99.94%	71.56%
Controlled Decision Tree	81.39%	79.89%
Role of max_depth

The max_depth parameter limits how deep the decision tree can grow. Restricting the tree to a maximum depth of 5 prevents it from creating too many branches and memorizing the training data. This reduces the model's variance and improves its ability to generalize to unseen data, although it may introduce a small amount of bias because the model cannot capture every detail in the training set.

Role of min_samples_split
============================

The min_samples_split parameter specifies the minimum number of samples required to split a node. By setting min_samples_split = 20, the tree avoids creating splits based on very small groups of observations. This reduces the chance of learning random noise from the training data and results in a more robust model.

Comparison with the Baseline Decision Tree
==========================================

The baseline Decision Tree achieved 99.94% training accuracy but only 71.56% test accuracy, indicating severe overfitting because the model almost memorized the training data. In contrast, the controlled Decision Tree achieved 81.39% training accuracy and 79.89% test accuracy. The much smaller gap between training and test accuracy demonstrates that the controlled model generalizes better to unseen data. Although the training accuracy decreased, the improved test accuracy indicates that limiting the tree depth and requiring more samples for splitting successfully reduced overfitting and produced a more reliable classification model.

Gini vs Entropy Comparison
==========================
Two Decision Tree models were trained using the same tree depth (max_depth = 5) but different splitting criteria.

Criterion	Test Accuracy
Gini	79.94%
Entropy	79.34%

The Gini criterion achieved slightly higher test accuracy than Entropy, indicating marginally better classification performance for this dataset.

Gini Impurity Formula
=======================
Gini=1−∑pi2
where pi = probability of class i in a node.

Gini impurity measures how mixed the classes are within a node. A lower Gini value indicates a purer node.

Entropy Formula
===============
Entropy=−∑pilog2(pi)
where:pi= probability of class i in a node.

Entropy measures the amount of uncertainty or disorder in a node. Lower entropy indicates less uncertainty and a purer node.

What does Gini = 0 mean?

A node with Gini = 0 means that all samples in the node belong to the same class. Such a node is called a pure node because there is no class impurity or uncertainty.

Example

Suppose a node contains 10 samples:

High Revenue	Low Revenue
10	0

Then:

p(High)=1,p(Low)=0
Gini=1−(12+02)=0

Since every sample belongs to the same class, no further splitting is required.

Interpretation
==============
Both Gini and Entropy aim to create pure nodes by selecting the best feature split. In this project, the Gini criterion produced a slightly higher test accuracy (79.94%) than Entropy (79.34%), making it the preferred splitting criterion for this dataset.

Part 3: Task 4 :Random Forest Classifier
========================
The Random Forest model achieved the following performance:

Metric	Value
===================
Training Accuracy	82.87%
Test Accuracy	79.89%
ROC-AUC	0.8703

The small difference between the training and test accuracy indicates that the Random Forest model generalizes well and does not show significant overfitting.

Top 5 Important Features
=========================
Rank	Feature	Importance
1	Budget	0.6403
2	Runtime	0.1285
3	Release Year	0.0657
4	Release Month	0.0291
5	Adventure	0.0117

Part 3: Task 4a : Gradient Boosting
==================================

Gradient Boosting Classifier
============================
A Gradient Boosting Classifier was trained using the following parameters:

n_estimators = 100
learning_rate = 0.1
max_depth = 3
random_state = 42
Model Performance
Metric	Value
Training Accuracy	81.81%
Test Accuracy	80.12%
ROC-AUC	0.8732

The training and test accuracies are very close, indicating that the model generalizes well and does not show significant overfitting. The ROC-AUC score of 0.8732 indicates that the model has a good ability to distinguish between movies with above-median and below-median revenue.

How Gradient Boosting Works

Gradient Boosting builds an ensemble of weak decision trees sequentially. Unlike Random Forest, where all trees are built independently, each new tree in Gradient Boosting is trained to correct the errors made by the previous trees. The model starts with an initial prediction, calculates the prediction errors (residuals), and then fits a new tree to those residuals. This process is repeated for 100 trees (n_estimators = 100), gradually improving the model's predictions.

The learning rate (learning_rate = 0.1) controls how much each new tree contributes to the final prediction. A smaller learning rate makes learning more gradual and helps reduce overfitting. The maximum depth (max_depth = 3) limits the complexity of each individual tree, ensuring that each tree remains a weak learner while the ensemble collectively captures complex patterns.

Comparison with Other Models
==============================
Model	Training Accuracy	Test Accuracy	ROC-AUC
Decision Tree (Baseline)	99.94%	71.56%	–
Decision Tree (Controlled)	81.39%	79.89%	–
Random Forest	82.87%	79.89%	0.8703
Gradient Boosting	81.81%	80.12%	0.8732

Among the evaluated tree-based models, Gradient Boosting achieved the highest test accuracy and the highest ROC-AUC score, indicating the best overall classification performance. Its sequential learning strategy enables it to correct previous prediction errors, resulting in better generalization while avoiding the severe overfitting observed in the baseline Decision Tree.

Part 3: Task 4b: Feature Ablation Study
========================================
A feature ablation study was performed by removing the five least important features identified by the Random Forest model:

War
Western
Music
History
Mystery

A second Random Forest model was trained using the same hyperparameters after removing these features.

ROC-AUC Comparison
=======================
Model	ROC-AUC
Full Random Forest	0.8732
Reduced Random Forest	0.8730

Part 3: Task 5:Cross-Validated Model Comparison
=============================================

The performance of four classification models was evaluated using 5-fold Stratified Cross-Validation with ROC-AUC as the evaluation metric.

Model	Mean ROC-AUC	Std ROC-AUC
===================================
Logistic Regression	0.8408	0.0072
Decision Tree (Controlled)	0.8587	0.0065
Random Forest	0.8705	0.0071
Gradient Boosting	0.8752	0.0076

The Gradient Boosting model achieved the highest average ROC-AUC (0.8752), followed closely by Random Forest (0.8705). All four models have relatively small standard deviations (around 0.006–0.008), indicating consistent performance across the five folds.

Why Cross-Validation is More Reliable
======================================

A single train-test split evaluates the model using only one particular partition of the dataset. The measured performance may vary depending on which samples happen to fall into the training and testing sets. In contrast, 5-fold cross-validation repeatedly trains and evaluates the model on different subsets of the data. Each observation is used for both training and validation across different folds, resulting in a more representative estimate of how the model will perform on unseen data. Reporting both the mean ROC-AUC and the standard deviation provides a more reliable assessment of model generalization and stability than relying on a single train-test split.

Interpretation
===============
The ROC-AUC decreased only slightly from 0.8732 to 0.8730, a difference of 0.0002. This change is negligible, indicating that the removed features contributed very little to the model's predictive performance. Therefore, these features can be considered largely uninformative for this classification task, and removing them does not significantly affect the model's ability to distinguish between high-revenue and low-revenue movies.

Part 3 : Task 6 Hyperparameter Tuning using GridSearchCV
=========================================================
A GridSearchCV was performed to identify the best combination of hyperparameters for the Random Forest classifier.

Best Hyperparameters
======================
Hyperparameter	Best Value
n_estimators	200
max_depth	None
min_samples_leaf	5
Best Cross-Validated ROC-AUC

ROC-AUC = 0.8708

This indicates that the tuned Random Forest model achieved a strong ability to distinguish between high-revenue and low-revenue movies during 5-fold cross-validation.

Number of Model Configurations Evaluated

The parameter grid contained:
==============================
3 values for n_estimators → (50, 100, 200)
3 values for max_depth → (5, 10, None)
2 values for min_samples_leaf → (1, 5)

Total hyperparameter combinations:3×3×2=18

Using 5-fold cross-validation, each combination was trained and evaluated 5 times.

Therefore, the total number of model fits was: 18×5=90

So, GridSearchCV evaluated 18 different hyperparameter combinations, resulting in a total of 90 model training and evaluation runs.

Grid Search vs Randomized Search
==================================
Grid Search evaluates every possible combination of hyperparameters in the specified grid. This guarantees that the best combination within the defined search space is found, but it can become computationally expensive as the number of parameters and candidate values increases.

Randomized Search, on the other hand, evaluates only a random subset of the possible combinations. It requires much less computation time and is often preferred for large search spaces. Although it does not guarantee finding the absolute best combination, it frequently identifies a near-optimal solution while significantly reducing training time.

Part 3 - Task 7 - Manual Learning Curve
=========================================
The learning curve was generated by training the best Random Forest pipeline on progressively larger portions of the training data (20%, 40%, 60%, 80%, and 100%) and evaluating both the Training AUC and Test AUC.

Training Fraction	Training AUC	Test AUC
20%	0.9289	0.8590
40%	0.9316	0.8662
60%	0.9326	0.8671
80%	0.9319	0.8687
100%	0.9306	0.8696
(i) Does the Training AUC decrease as the training set grows?

The Training AUC remains fairly stable, varying only between 0.9289 and 0.9326. It does not show a significant decrease as more training data is added. This indicates that the tuned Random Forest model is not strongly overfitting small training subsets.

(ii) Does the Test AUC increase with more training data?

Yes. The Test AUC increases gradually from 0.8590 at 20% of the training data to 0.8696 when using the full training dataset. This indicates that the model benefits from additional training data and its ability to generalize improves as more examples are available.

(iii) Conclusion

The Test AUC continues to increase slightly even when the full training dataset is used, although the improvement becomes smaller as the training size grows. This suggests that the model is still somewhat limited by the amount of available training data rather than by model capacity. Collecting additional high-quality training data could provide further improvements, but the gradual flattening of the curve also indicates that the model is beginning to approach its current performance limit.

Part 3 - Task 8 - Model Serialization
=====================================
Model Serialization

The best-performing pipeline obtained from GridSearchCV was serialized using the Joblib library.

joblib.dump(best_pipeline, "best_model.pkl")

The saved pipeline was successfully reloaded using:

loaded_model = joblib.load("best_model.pkl")

Two manually created movie records were passed to the reloaded model using the .predict() method. The model generated predictions successfully without any errors, confirming that the serialized pipeline can be restored and used for inference.

Since best_model.pkl is well below GitHub's 100 MB file size limit for this project, it can be committed directly to the repository along with the notebook.

Part 3 - Task 9 -Summary Comparison Table
==========================================
Model	5-Fold CV Mean ROC-AUC	5-Fold CV Std ROC-AUC	Test ROC-AUC
Logistic Regression	0.8408	0.0072	0.8356
Decision Tree (Controlled)	0.8587	0.0065	Not computed
Random Forest	0.8705	0.0071	0.8703
Gradient Boosting	0.8752	0.0076	0.8732

Note: The controlled Decision Tree was evaluated using training and test accuracy but not ROC-AUC on the test set. Therefore, the test ROC-AUC is not available unless you explicitly compute it.

Recommended Model
====================

Gradient Boosting is the recommended model for deployment.

Justification
=================
Gradient Boosting achieved the highest cross-validated ROC-AUC (0.8752) and the highest test ROC-AUC (0.8732) among all evaluated models, indicating the strongest ability to distinguish between high-revenue and low-revenue movies. It also maintained similar training and test accuracy, suggesting good generalization without significant overfitting. Compared with Logistic Regression, Decision Tree, and Random Forest, Gradient Boosting consistently delivered the best overall predictive performance. Therefore, it is the most suitable model for deployment because it provides the best balance between accuracy, robustness, and generalization on unseen data.

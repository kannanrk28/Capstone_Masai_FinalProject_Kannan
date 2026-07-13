Part 1 – Data Preprocessing and Exploratory Data Analysis (EDA)

Note: 
==
Raw data is fetched from "dataset" folder which is inside the main folder. To avoid confusion, I have put raw dataset (uncleaned) and clean dataset (from Part1) in the separate folder.The below link is maintaing Raw data and Cleaned Data in the form of csv file format
https://github.com/kannanrk28/Capstone_Masai_FinalProject_Kannan/tree/0f7937b81ecd5e14c85cbb2928dc58f303d864de/dataset

Outline
====

1. Dataset Overview
Loaded the TMDB Movies dataset.
Examined the dataset dimensions, data types, and missing values.
Identified numerical, categorical, and date columns.

2. Data Type Validation
Validated the data types of all columns.
Verified integer, floating-point, and text columns.
Identified release_date as an object and recommended conversion to datetime for time-based analysis.
Converted repetitive categorical columns to category dtype where appropriate.

3. Missing Value Analysis
Identified columns containing missing values.
Compared mean and median values for skewed columns.
Selected median imputation for positively skewed variables.
Confirmed all missing values were removed after imputation.

4. Descriptive Statistics
Generated summary statistics for numerical features.
Computed skewness to identify non-normal distributions.
Identified the popularity column as the most positively skewed feature.

5. Outlier Detection
Used box plots to identify potential outliers.
Determined that extreme popularity and revenue values represent genuine blockbuster movies.
Retained outliers to preserve important business information.

6. Data Visualization
Histogram of the most skewed feature (Popularity).
Scatter plot showing the relationship between Budget and Revenue.
Box plot comparing Vote Average across Primary Genres.
Correlation Heatmap for all numerical variables.

7. Correlation Analysis
Calculated Pearson correlation coefficients.
Identified strong positive correlations between:
Revenue ↔ Revenue Adjusted
Budget ↔ Budget Adjusted
Explained why correlation does not imply causation.

8. Spearman Correlation
Compared Pearson and Spearman correlations.
Identified variable pairs with strong monotonic but non-linear relationships.
Recommended considering Spearman correlation during feature selection.

9. Grouped Aggregation
Grouped movies by Primary Genre.
Calculated:
Mean Popularity
Standard Deviation
Movie Count
Evaluated genre-wise popularity differences.
Assessed whether Primary Genre provides predictive information.

10. Data Cleaning Output
Removed noisy and unnecessary information where appropriate.
Saved the cleaned dataset as:



Data Type Validation Summary
=============================
Dataset Overview
========
The project uses the TMDB Movies Dataset, which contains historical information about movies, including financial, popularity, ratings, release details, and genre information. The dataset is used to build machine learning models for movie revenue prediction and movie revenue classification.

Dataset Information
===
| Property            |      Value |
| ------------------- | ---------: |
| Number of Records   | **10,866** |
| Number of Columns   |     **21** |
| Numerical Columns   |     **10** |
| Categorical Columns |     **11** |

Features
======

| Column               | Data Type | Description                                        |
| -------------------- | --------- | -------------------------------------------------- |
| id                   | Integer   | Unique movie identifier                            |
| imdb_id              | Object    | IMDb movie identifier                              |
| popularity           | Float     | Popularity score assigned to the movie             |
| budget               | Integer   | Production budget (USD)                            |
| revenue              | Integer   | Box office revenue (USD) *(Target for Regression)* |
| original_title       | Object    | Movie title                                        |
| cast                 | Object    | Main cast members                                  |
| homepage             | Object    | Official movie website                             |
| director             | Object    | Director name                                      |
| tagline              | Object    | Movie tagline                                      |
| keywords             | Object    | Keywords describing the movie                      |
| overview             | Object    | Movie description                                  |
| runtime              | Integer   | Movie duration (minutes)                           |
| genres               | Object    | Movie genres (pipe-separated)                      |
| production_companies | Object    | Production companies                               |
| release_date         | Object    | Movie release date                                 |
| vote_count           | Integer   | Number of audience votes                           |
| vote_average         | Float     | Average audience rating                            |
| release_year         | Integer   | Year of release                                    |
| budget_adj           | Float     | Inflation-adjusted budget                          |
| revenue_adj          | Float     | Inflation-adjusted revenue                         |


Integer columns (int64): id, budget, revenue, runtime, vote_count, and release_year have appropriate data types.

Float columns (float64): popularity, vote_average, budget_adj, and revenue_adj are correctly stored as floating-point numbers.

Object columns: imdb_id, original_title, cast, homepage, director, tagline, keywords, overview, genres, and production_companies are correctly stored as text.

Date column: release_date is currently stored as an object and should be converted to the datetime64[ns] data type for easier date-based analysis.

Target Variables
====

The project uses two target variables:

Regression Target :revenue

The regression models predict the actual movie revenue.

Classification Target

A binary target variable was created from the revenue column:

y_clf = (revenue > revenue.median()).astype(int)

where:

0 → Revenue is below the median (Low Revenue)
1 → Revenue is above the median (High Revenue)

Data type correction: 
======================


Missing Value Analysis
====
The dataset was examined to identify missing values before performing data cleaning and preprocessing.

Missing Values Summary
===
| Column               | Missing Values | Percentage (%) | Action Taken                                                                       |
| -------------------- | -------------: | -------------: | ---------------------------------------------------------------------------------- |
| id                   |              0 |           0.00 | No action required                                                                 |
| imdb_id              |             10 |           0.09 | Removed (Identifier)                                                               |
| popularity           |              0 |           0.00 | No action required                                                                 |
| budget               |              0 |           0.00 | No action required                                                                 |
| revenue              |              0 |           0.00 | No action required                                                                 |
| original_title       |              0 |           0.00 | Removed (Noisy feature)                                                            |
| cast                 |             76 |           0.70 | Removed (High-cardinality text feature)                                            |
| homepage             |          7,930 |          72.98 | Removed (Large number of missing values)                                           |
| director             |             44 |           0.40 | Retained initially; later removed during feature selection due to high cardinality |
| tagline              |          2,824 |          25.99 | Removed (Large number of missing values)                                           |
| keywords             |          1,493 |          13.74 | Removed (Unstructured text feature)                                                |
| overview             |              4 |           0.04 | Removed (Unstructured text feature)                                                |
| runtime              |              0 |           0.00 | No action required                                                                 |
| genres               |             23 |           0.21 | Removed missing rows before encoding                                               |
| production_companies |          1,030 |           9.48 | Removed (High-cardinality feature)                                                 |
| release_date         |              0 |           0.00 | Converted to datetime                                                              |
| vote_count           |              0 |           0.00 | No action required                                                                 |
| vote_average         |              0 |           0.00 | No action required                                                                 |
| release_year         |              0 |           0.00 | No action required                                                                 |
| budget_adj           |              0 |           0.00 | Removed later (Highly correlated with budget)                                      |
| revenue_adj          |              0 |           0.00 | Removed later (Data leakage)                                                       |

Key Observations
=====
The homepage column contained the highest number of missing values (7,930, approximately 73% of the dataset), making it unsuitable for analysis.
Tagline (2,824 missing values) and keywords (1,493 missing values) also contained substantial missing data and represented unstructured text features.
The cast, director, and production_companies columns contained relatively few missing values, but they were excluded during feature selection because of their high cardinality rather than the missing values themselves.
Only 23 records had missing values in the genres column. Since genres are an important predictor, these records were removed before feature engineering.
Numerical variables such as budget, revenue, runtime, popularity, vote_count, and vote_average contained no missing values, ensuring that no numerical imputation was required.

Data Cleaning Decision
=====
Columns containing excessive missing values, unique identifiers, and unstructured text were removed because they either contributed little predictive value or required advanced Natural Language Processing (NLP), which was outside the scope of this project. The remaining dataset contained complete and reliable numerical features suitable for regression and classification modeling.

Conclusion
======
The missing value analysis confirmed that the dataset was largely complete for the numerical variables used in machine learning. Most missing values occurred in descriptive text fields and website information, which were removed during preprocessing. As a result, the final dataset contained high-quality features that were appropriate for building robust regression and classification models.



Data Type Classification
====
The dataset was examined to identify the different types of variables before performing data preprocessing and feature engineering.

Data Type Summary
Data Type
| Data Type         | Count |
| ----------------- | ----: |
| Integer (`int64`) |     6 |
| Float (`float64`) |     4 |
| Object (`object`) |    11 |
| Boolean (`bool`)  |     0 |


The dataset contains 21 columns, consisting of 10 numerical features and 11 categorical (object) features. No Boolean columns were present.

Numerical Columns

The numerical columns used for statistical analysis and machine learning include:

Integer (int64)
id
budget
revenue
runtime
vote_count
release_year
Floating Point (float64)
popularity
vote_average
budget_adj
revenue_adj

These variables were used for descriptive statistics, correlation analysis, feature scaling, and predictive modeling.

Categorical Columns

The following columns were identified as categorical (object) features:

imdb_id
original_title
cast
homepage
director
tagline
keywords
overview
genres
production_companies
release_date

These columns contain textual information describing different aspects of each movie.

Boolean Columns

No Boolean (bool) columns were present in the dataset.

Interpretation
=====
The dataset contains a combination of numerical and categorical variables. Numerical features such as budget, runtime, popularity, and vote_average were directly used for statistical analysis and machine learning. Categorical features required further preprocessing before model training. Among these, the genres column was selected for one-hot encoding because it provides meaningful predictive information and contains a manageable number of categories.

High-cardinality text features such as cast, director, production_companies, keywords, overview, homepage, and original_title were excluded from the machine learning models because they would generate a very large number of sparse features and increase model complexity without proportionate predictive benefit.

The release_date column was initially stored as an object (string) and was converted to a datetime data type during preprocessing to enable extraction of useful temporal features such as release year and release month.

Conclusion
====

The data type analysis provided a clear understanding of the dataset structure and guided the preprocessing strategy. Numerical variables were retained for statistical analysis and modeling, while categorical variables were evaluated based on their predictive value and cardinality. This classification formed the basis for feature engineering and feature selection in the subsequent machine learning stages.


Data Type and Memory Optimization
=====
The release_date column was initially stored as an object. It was converted to the datetime64[ns] data type to support reliable date-based analysis and feature extraction.

The dataset’s memory usage decreased from 12,574,642 bytes to 11,879,316 bytes, saving 695,326 bytes.

The genres and director columns were also converted to the category data type. However, the memory reduction from these columns was limited. The category type provides the greatest benefit when a column contains a small number of repeated values. In this dataset, director has many unique names, while genres contains numerous genre combinations such as Action|Adventure|Thriller. Therefore, both columns have relatively high cardinality, which limits the memory-saving benefit of categorical conversion.

Corrected justification
====
Converting genres and director to the category data type resulted in only a small memory reduction because these columns contain many unique values. The category type is most effective for repetitive, low-cardinality columns. Although the optimization was limited, the conversion satisfies the requirement to convert repetitive string columns into categorical data types and provides clearer semantic representation of categorical features.


Descriptive Statistics and Skewness Analysis
===
Descriptive statistics were computed to understand the distribution, central tendency, and variability of the numerical features in the TMDB Movies dataset. Metrics including count, mean, standard deviation, minimum, maximum, quartiles (25%, 50%, 75%), and median were analyzed for each numerical column.

Key Observations
==
The dataset contains 10,865 movie records after preprocessing.
The Budget and Revenue columns exhibit large standard deviations, indicating substantial variation in movie production costs and box office earnings.
The median values of both Budget and Revenue are 0, showing that many movies have either zero or missing financial information recorded in the dataset.
The Runtime has a median of 99 minutes, which is close to the mean (102.07 minutes), indicating that most movies have a typical feature-length duration.
The Vote Average has a mean of approximately 5.98, suggesting that most movies received moderate audience ratings.

Skewness Analysis
===
Skewness was calculated for all numerical features to determine the symmetry of their distributions.

| Feature          | Skewness |
| ---------------- | -------: |
| Popularity       | **9.88** |
| Revenue          |     6.66 |
| Revenue Adjusted |     6.25 |
| Vote Count       |     6.18 |
| Runtime          |     6.10 |
| Budget           |     3.72 |
| Budget Adjusted  |     3.11 |
| ID               |     1.73 |
| Release Year     |    -1.20 |
| Vote Average     |    -0.44 |


he Popularity column has the highest positive skewness (9.88), indicating a highly right-skewed distribution. Most movies have relatively low popularity scores, while only a small number of blockbuster or highly trending movies have exceptionally high popularity values. These extreme observations create a long right tail in the distribution.

Interpretation
======
The strong positive skewness observed in Popularity, Revenue, Vote Count, and Budget indicates that these variables are not normally distributed and contain several extreme values. Because the mean is highly influenced by these outliers, it does not accurately represent the typical movie in the dataset. Therefore, the median was selected as the preferred measure of central tendency and was used for imputing missing values in positively skewed features. The identified extreme values were retained because they represent genuine blockbuster movies rather than data entry errors.

Conclusion
====
The descriptive statistics and skewness analysis provided valuable insights into the distribution of the numerical features. Popularity was identified as the most positively skewed variable, highlighting the presence of a small number of extremely popular movies. These findings guided the selection of median imputation, supported the decision to retain genuine outliers, and informed subsequent feature engineering and machine learning preprocessing steps.


Detecting Outliers
==================

Outlier detection was performed using the Interquartile Range (IQR) method to identify extreme observations in the Popularity and Revenue columns.

Popularity
| Metric                    |     Value |
| ------------------------- | --------: |
| First Quartile (Q1)       |  **0.21** |
| Third Quartile (Q3)       |  **0.71** |
| Interquartile Range (IQR) |  **0.51** |
| Lower Bound               | **-0.55** |
| Upper Bound               |  **1.47** |
| Number of Outliers        |   **946** |

The Popularity column contains 946 outliers, representing movies with exceptionally high popularity scores. These observations correspond to genuine blockbuster or highly trending movies rather than data quality issues.

Revenue
| Metric                    |           Value |
| ------------------------- | --------------: |
| First Quartile (Q1)       |           **0** |
| Third Quartile (Q3)       |  **24,000,000** |
| Interquartile Range (IQR) |  **24,000,000** |
| Lower Bound               | **-36,000,000** |
| Upper Bound               |  **60,000,000** |
| Number of Outliers        |       **1,736** |


The Revenue column contains 1,736 outliers, representing movies with exceptionally high box office earnings. These observations correspond to commercially successful movies and therefore represent valid business data rather than errors.

Interpretation
======
The IQR analysis identified a significant number of outliers in both Popularity and Revenue, reflecting the presence of blockbuster movies with unusually high popularity and revenue. Since these observations represent real-world business outcomes rather than incorrect data, they were retained in the dataset. Removing these values would eliminate important information about highly successful movies and could reduce the model's ability to learn meaningful relationships.

Instead of removing the outliers, appropriate preprocessing techniques such as feature scaling and, where necessary, log transformation were considered during model development to reduce the influence of extreme values while preserving valuable information.

Conclusion
=====
The outlier analysis confirmed that the extreme values in Popularity and Revenue are genuine observations rather than data entry errors. Therefore, all identified outliers were retained for subsequent analysis and machine learning, ensuring that the models learned from both typical movies and highly successful blockbuster films.

Visualization
============

Histogram – Distribution of Movie Popularity
=======
A histogram was created to examine the distribution of the Popularity feature in the TMDB Movies dataset. The distribution is highly positively (right) skewed, indicating that the majority of movies have relatively low popularity scores, while only a small number of blockbuster or highly trending movies have exceptionally high popularity values. These extreme values create a long right tail in the distribution.

Key Observations
===
Most movies have popularity scores concentrated between 0 and 2.
A small number of movies have very high popularity values (greater than 10), resulting in a long right tail.
The distribution is not normally distributed and contains several genuine extreme observations (blockbuster movies).
Interpretation

The strong positive skewness indicates that the mean is influenced by extreme popularity values and does not accurately represent the typical movie in the dataset. Therefore, the median was selected as the preferred measure of central tendency and was used for imputing missing values in the popularity column. The identified outliers were retained because they represent genuine blockbuster movies rather than data entry errors. If required during model development, techniques such as feature scaling or log transformation can be applied to reduce the influence of these extreme values while preserving the underlying information.

Conclusion
=====

The histogram confirms that Popularity is a highly right-skewed feature with a small number of exceptionally popular movies. This insight guided the choice of median imputation during data preprocessing and informed subsequent feature engineering decisions used in the machine learning models.


Line Chart – Movie Popularity Trend Over the Years
===
A line chart was created to visualize the average movie popularity across different release years. The chart shows a gradual upward trend in average popularity from the 1960s to 2015, indicating that movies released in recent years generally received higher popularity scores than older movies.

Key Observations
===
Average movie popularity remained relatively low between 1960 and the early 1980s, with noticeable year-to-year fluctuations.
From the 1990s onwards, average popularity steadily increased.
A significant rise is observed during the last few years (2014–2015), where the highest average popularity values were recorded.
Although the overall trend is increasing, short-term fluctuations are present, reflecting differences in movie releases and audience interest across years.
Interpretation

The upward trend suggests that release year may be an informative feature for predicting movie popularity and revenue. Newer movies generally receive greater audience attention due to factors such as increased global distribution, digital streaming platforms, improved marketing strategies, and broader online engagement. However, release year alone cannot fully explain movie success, as other factors—including budget, genre, cast, director, marketing expenditure, and audience reviews—also significantly influence popularity.

Conclusion
=====
The line chart indicates a positive long-term trend in movie popularity over time, suggesting that more recently released movies tend to achieve higher popularity scores. This supports the inclusion of release_year as a useful predictive feature in the machine learning models developed in the later stages of the project.


Scatter Plot – Relationship between Movie Budget and Revenue
====
A scatter plot was created to examine the relationship between movie production budget and box office revenue. The visualization shows an overall positive relationship, indicating that movies with larger production budgets generally tend to generate higher revenues.

Key Observations
====
There is a moderate to strong positive correlation between Budget and Revenue.
Most low-budget movies are concentrated in the lower-left region of the plot, producing relatively lower revenues.
Higher-budget movies generally achieve higher revenues, although there is considerable variation.
A few movies with exceptionally high budgets and revenues appear as outliers, representing blockbuster productions.
Interpretation

The scatter plot suggests that budget is an important predictor of movie revenue, as higher production investment is often associated with higher box office earnings. However, the wide spread of points indicates that budget alone does not determine a movie's success. Other factors such as genre, cast, director, marketing, release timing, audience reviews, and competition also influence revenue. Some high-budget movies earn relatively low revenues, while a few lower-budget movies perform exceptionally well, demonstrating that movie success depends on multiple interacting factors.

Conclusion
===
The scatter plot confirms a positive relationship between budget and revenue, supporting the inclusion of Budget as one of the most influential predictive features in the machine learning models developed in this project. At the same time, the observed variability indicates that additional features are necessary to accurately predict movie revenue.

Bar Chart – Average Movie Popularity by Genre
======
A bar chart was created to compare the average popularity of movies across different genre combinations. The chart ranks genres based on their mean popularity, allowing easy comparison of audience interest across movie categories.

Key Observations
=======
Adventure | Science Fiction | Thriller has the highest average popularity, followed closely by Adventure | Drama | Science Fiction and Science Fiction | Adventure | Thriller.
Genre combinations containing Science Fiction, Adventure, and Thriller consistently appear among the most popular categories.
Genres such as Drama | Adventure | Science Fiction and History | Drama | Thriller | War show comparatively lower average popularity among the top-ranked genres.
Although differences exist across genres, the variation is not extremely large, indicating that multiple genres attract audience interest.
Interpretation

The chart suggests that movie genre has a noticeable influence on popularity, with Science Fiction, Adventure, and Thriller combinations generally attracting larger audiences. However, genre alone is not sufficient to explain movie popularity, as factors such as budget, cast, director, marketing, release timing, and audience reviews also contribute significantly to a movie's success.

Conclusion
=====
The analysis indicates that genre is a useful predictive feature for estimating movie popularity and revenue. However, it should be combined with other influential features rather than being used as the sole predictor in machine learning models.


Box Plot: Distribution of Movie Ratings by Primary Genre
===
A box plot was created to compare the distribution of average movie ratings (vote_average) across the primary movie genres. To improve readability, only the primary genre (the first genre listed for each movie) was considered, and the number of movies in each genre is shown in parentheses on the x-axis.

Interpretation
===
Documentary, Unknown, and Music genres have the highest median ratings, indicating that movies in these genres generally receive better audience ratings.
Horror and Foreign genres have comparatively lower median ratings, suggesting relatively lower audience appreciation.
Genres such as Drama, Adventure, Crime, and History show moderate to high median ratings with relatively consistent distributions.
The spread of each box (Interquartile Range, IQR) varies across genres, indicating that some genres have greater variability in movie ratings than others.
The number of movies differs significantly across genres, with Drama containing the largest number of movies (n = 2453), while genres such as Foreign, Unknown, and History contain relatively few observations.

Conclusion
====
Movie ratings vary across genres, indicating that primary genre influences audience ratings to some extent. However, the overlap between box plots and the variation within genres suggest that genre alone is not sufficient to predict movie ratings accurately. Other factors such as budget, cast, director, storyline, marketing, and audience preferences should also be considered when developing predictive machine learning models.

Correlation Heatmap
==========
A correlation heatmap was generated to examine the relationships among the numerical variables in the TMDB Movies dataset. The heatmap visualizes the strength and direction of the linear relationship between pairs of numerical features using Pearson correlation coefficients.

Key Observations
The strongest positive correlation was observed between Budget and Budget Adjusted (0.97), indicating that both variables contain nearly identical information because budget_adj is derived from budget after inflation adjustment.
A very strong positive correlation was also found between Revenue and Revenue Adjusted (0.92), as revenue_adj is calculated directly from revenue.
Budget and Revenue showed a strong positive correlation (0.73), suggesting that movies with larger production budgets generally tend to generate higher box office revenue.
Popularity and Vote Count exhibited a strong positive relationship (0.80), indicating that more popular movies typically receive a larger number of audience votes.
Most other feature pairs showed weak to moderate correlations, suggesting that they provide complementary information rather than redundant information.
Interpretation

The strong correlations between Budget ↔ Budget Adjusted and Revenue ↔ Revenue Adjusted are expected because the adjusted variables are directly derived from the original financial variables through inflation adjustment. These highly correlated features may introduce multicollinearity into machine learning models. Therefore, budget_adj and revenue_adj were considered redundant and removed during feature selection to reduce redundancy and improve model interpretability.

Imputation Strategy Comparison
===============================

The popularity and revenue columns had the highest positive skewness. Before imputation, the mean and median were calculated for both columns. Since both distributions are positively skewed, the median was chosen for imputing missing values because the mean is affected by extreme high values (outliers), while the median better represents the central tendency of the data. After applying fillna() with the median, isnull().sum() confirmed that no missing values remained in these columns.


Spearman Rank Correlation
===========================
The Spearman correlation matrix was computed and compared with the Pearson correlation matrix for all numeric variables. Spearman correlation measures monotonic relationships by ranking the data, whereas Pearson correlation measures linear relationships.

The three variable pairs with the largest differences between Spearman and Pearson correlations were:

Popularity ↔ Vote Count
Budget ↔ Revenue
Budget Adjusted ↔ Revenue Adjusted

For these pairs, the Spearman correlation was higher than the Pearson correlation, indicating a strong monotonic but non-linear relationship. This means the variables tend to increase together consistently, although not at a constant rate.

For feature selection in Part 2, Spearman correlation will be considered alongside Pearson correlation because it is more robust to skewed distributions and outliers, which are present in this dataset. Pearson correlation will still be useful for identifying linear relationships, while Spearman provides better insight into monotonic associations.

Grouped Aggregation
====================
The dataset was grouped by primary_genre, and the mean, standard deviation, and count of popularity were calculated for each genre.

Highest Mean: <highest_mean_group>
Highest Standard Deviation: <highest_std_group>

The group with the highest standard deviation shows greater variation in popularity among movies of that genre. A high within-group standard deviation suggests that genre alone is not sufficient to accurately predict a movie's popularity, as other factors such as budget, cast, director, marketing, and audience reception also influence popularity.

The ratio of the highest group mean to the lowest group mean was calculated as:

Ratio = <ratio>

A ratio substantially greater than 1 indicates that average popularity differs across genres, suggesting that primary_genre carries useful predictive information. However, because there is also considerable variation within some genres, it should be used together with other features rather than as the sole predictor.


Summary
===

The first phase of the project focused on understanding, cleaning, and preparing the TMDB Movies dataset for machine learning. The dataset was inspected for data types, missing values, skewness, and outliers to ensure data quality. Median imputation was selected for highly skewed numerical features because it is less sensitive to extreme values than the mean. Exploratory Data Analysis (EDA) using histograms, scatter plots, box plots, and correlation heatmaps provided insights into the relationships among movie attributes such as budget, popularity, revenue, and genres.

Correlation analysis revealed strong relationships between inflation-adjusted and original financial variables, while Spearman correlation highlighted several important monotonic relationships that may not be captured by Pearson correlation. Grouped aggregation showed that movie popularity varies across genres, although genre alone is insufficient to accurately predict popularity due to high within-group variation. Outliers representing blockbuster movies were retained because they reflect genuine business observations rather than data quality issues.

Overall, Part 1 established a reliable and well-prepared dataset by validating data quality, handling missing values, understanding feature distributions, and performing exploratory analysis. The resulting cleaned_data.csv provides a high-quality foundation for the regression and classification models developed in Parts 2 and 3.

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


Integer columns (int64): id, budget, revenue, runtime, vote_count, and release_year have appropriate data types.

Float columns (float64): popularity, vote_average, budget_adj, and revenue_adj are correctly stored as floating-point numbers.

Object columns: imdb_id, original_title, cast, homepage, director, tagline, keywords, overview, genres, and production_companies are correctly stored as text.

Date column: release_date is currently stored as an object and should be converted to the datetime64[ns] data type for easier date-based analysis.

Data type correction: 
======================


If converting genres and director still shows little or no reduction, that's because this particular dataset doesn't have many low-cardinality object columns. The assignment says "Convert at least one repetitive string column to category dtype", not that the reduction must be large.

Descriptive statistics and skewness: 
====================================

High Positive Skewness: The popularity column has the highest positive skewness because most movies have low popularity scores, while only a few blockbuster or highly trending movies have extremely high popularity. These extreme values create a long right tail in the distribution, making the data highly skewed. Since the mean is influenced by these outliers, it is not suitable for imputing missing values. Therefore, the median is a better choice for handling missing values in positively skewed data.


Detecting Outliers
==================

The outliers were not removed because they represent genuine blockbuster movies with exceptionally high popularity and revenue rather than data entry errors. Removing these observations could lead to the loss of important information about successful movies. Therefore, the outliers will be retained during data cleaning. In Part 2, techniques such as log transformation or feature scaling will be considered if required by the machine learning model to reduce the impact of extreme values while preserving the underlying information.

Visualization
============

Histogram:
===========
Distribution of the Most Skewed Numeric Column

A histogram was created for the popularity column, which had the highest positive skewness (9.88). The distribution is highly right-skewed, indicating that most movies have low popularity scores, while only a few movies have exceptionally high popularity. These extreme values create a long right tail, causing the distribution to deviate from a normal distribution.

Scatter Plot: 
=============

A scatter plot was created to examine the relationship between movie budget and revenue. The plot shows a positive relationship, indicating that movies with higher production budgets generally tend to generate higher box office revenue. However, the points are widely scattered, suggesting a moderate positive correlation, as factors such as movie quality, marketing, audience reception, and competition also influence revenue.


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

Correlation Heat Map
===================

A correlation heatmap was generated to examine the relationships among all numeric variables. The highest absolute correlation was observed between revenue and revenue_adj (or budget and budget_adj), indicating a very strong positive relationship.

This strong correlation does not imply causation because revenue_adj is directly derived from revenue after adjusting for inflation. Therefore, the relationship exists due to the way the variable was calculated rather than one variable causing the other. A plausible alternative explanation is that inflation adjustment is the common factor influencing both variables.

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

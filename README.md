# PowerOutages-Analysis

# Introduction 

The dataset my project is centered around is called [Major Power Outages Risks in the U.S.](https://engineering.purdue.edu/LASCI/research-data/outages). This dataset provides information on power outages across the United States, including details on the timing, location, causes, and impact of outages. It also includes data on regional climate conditions, electricity consumption, and economic factors, offering insights into how these elements relate to outage events.
Electricity has become a major component of everybody's life, therefore understanding **when** and for **what reasons** outages happen are crucial to be able to limit them. 
The dataset is made up of **1534** rows and **15** columns, the columns include:

| Column Name         | Description                                                                                                      |
|-----------------------|------------------------------------------------------------------------------------------------------------------|
| YEAR                  | Indicates the year when the outage event occurred.                                                              |
| U.S._STATE            | Represents all the states in the continental U.S.                                                                |
| OUTAGE.START.DATE     | This variable indicates the day of the year when the outage event started (as reported by the corresponding Utility in the region). |
| OUTAGE.RESTORATION.DATE | This variable indicates the day of the year when power was restored to all the customers (as reported by the corresponding Utility in the region). |
| OUTAGE.DURATION       | Duration of outage events (in minutes).                                                                          |
| CAUSE.CATEGORY        | Categories of all the events causing the major power outages.                                                   |
| CLIMATE.CATEGORY      | This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI). |
| ANOMALY.LEVEL         | Represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. It is estimated as a 3-month running mean of ERSST.v4 SST anomalies in the Niño 3.4 region. |
| HURRICANE.NAMES       | If the outage is due to a hurricane, then the hurricane name is given by this variable.                         |
| DEMAND.LOSS.MW        | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported].  |
| TOTAL.CUSTOMERS       | Annual number of total customers served in the U.S. state.                                                      |
| CUSTOMERS.AFFECTED    | Number of customers affected by the power outage event.                                                         |
| TOTAL.SALES           | Total electricity consumption in the U.S. state (megawatt-hour).                                                |
| TOTAL.PRICE           | Average monthly electricity price in the U.S. state (cents/kilowatt-hour).                                      |
| POPULATION            | Population in the U.S. state in a year.                                                                          |




# Data Cleaning and Exploratory Analysis 

## Data Cleaning

- The data cleaning process involved several key steps to ensure the dataset was well-structured and ready for analysis. First, unnecessary columns were removed, retaining only the most relevant features, such as outage details, cause and climate categories, customer impacts, and population data. This focused the dataset on the most important variables for understanding power outages. Next, the column names were standardized by renaming them to lowercase and providing more descriptive labels. This improved the clarity of the data, making it easier to work with and interpret in subsequent analyses.

- To address potential issues with missing or incorrectly formatted data, several transformations were applied. For example, the `DEMAND.LOSS.MW` column, which originally contained string values, was converted to a numeric type (float) to allow for proper calculations. The population data, initially in whole numbers, was transformed into thousands to facilitate easier comparison across different regions. The `ANOMALY.LEVEL` column, which contained null values, was handled by filling missing entries with zeros, ensuring consistency. Additionally, a new binary column, `BY_HURRICANE`, was introduced to flag whether an outage was caused by a hurricane, adding an important categorical variable for analysis.

- Finally, the dataset was further cleaned and structured by focusing on the most important variables, and any remaining inconsistencies or gaps were addressed through these transformations. These efforts ensured the data was in a usable format, with clear and consistent values that would support insightful analysis of power outages and their impacts across different regions and times.

**Below is the head of the table**

| year | US_state   | outage_start          | outage_len_minutes | is_hurricane | population_thousands |
|------|------------|-----------------------|--------------------|--------------|----------------------|
| 2011 | Minnesota  | 2011-07-01 17:00:00   | 3060.0             | False        | 5348.119             |
| 2014 | Minnesota  | 2014-05-11 18:38:00   | 1.0                | False        | 5457.125             |
| 2010 | Minnesota  | 2010-10-26 20:00:00   | 3000.0             | False        | 5310.903             |
| 2012 | Minnesota  | 2012-06-19 04:30:00   | 2550.0             | False        | 5380.443             |


___


## Exploratory Analysis 

### Univariate Analysis

- I created a histogram to see the distribution of the `anomaly_level` variable.
<iframe
  src="assets/anomaly_level_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analyis

- This box plot visualizes the distribution of demand lost during outages (in megawatts) across different causes of outages. By applying a logarithmic transformation to the demand loss values, the plot effectively highlights the variations in demand loss for each cause.
<iframe
  src="assets/box_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- This scatter plot shows the relationship between demand lost during outages (in megawatts) and the number of customers affected, with each point color-coded by the cause of the outage. Both axes use a logarithmic scale, making it easier to spot patterns across varying magnitudes.
<iframe
  src="assets/scatter_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

- This pivot table shows the connectedness of `Anomaly_level` and `climate_category`

| Climate Category | Anomaly Level | Outage Length (Minutes) |
|------------------|---------------|--------------------------|
| Cold             | -0.802537     | 2656.956803             |
| Normal           | -0.129839     | 2530.980822             |
| Warm             | 1.066558      | 2817.318021             |

___



# Assesment of Missingness 

## NMAR Analysis
I believe that `outage_len_in_minutes` is **NMAR** (Not Missing At Random), as the missing values for this column could be influenced by situations where the end date and time were not reported, potentially due to the outage taking longer than anticipated to resolve. If additional data, such as detailed logs of repair efforts were available, it might help clarify missingness.

## Missingness Dependencies 
The question I was addressing is whether the missing values in the `customers_affected` column follow any particular pattern related to the `year` column. Specifically, I wanted to test if the missingness of `customers_affected` is independent of the `year` or if there is a systematic pattern in the `year` for missing values.

<iframe
  src="assets/histogram_plot_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**NULL Hypothesis:** The distributions of `year` are the same whether `customers_affected` is missing or not.
**ALTERNATIVE Hypothesis:** The distributions of year for missing and non-missing customers_affected are different, suggesting that the missing data may follow a non-random pattern.

I performed the *Kolmogorov-Smirnov* (KS) test to compare the distribution of the `year` column between rows with **missing** (NaN) and **non-missing** values in `customers_affected`. 
To assess the significance of the observed KS statistic, I created a null distribution by randomly **permuting** the `year` column 10,000 times and calculating the KS statistic for each permutation. This distribution allowed me to compare the observed KS statistic to a random baseline, thus identifying that the observed value is significantly larger with `p_value = 0.0002`. Therefore I was able to reject the Null Hypothesis, meaning that `customers_affected` is MAR on `year`

<iframe
  src="assets/k2_s_test_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>



___


# Hypothesis Testing 

**NULL Hypothesis:** The average time to fix power outages is similar across areas, regardless of the number of customers in each area. In other words, the population size (number of customers) does not influence the time it takes to resolve an outage.
**ALTERNATIVE Hypothesis:**  Areas with more customers fix power outages more quickly than areas with fewer customers.

I created a categorical variable, that splits population down the middle, creating a higher and lower catagorie, that where I ran a permutation test and used *difference in means* as my test statistic. I got a `p_value = 0`, meaning that I was able to reject the NULL hypothesis. I chose these methods because they directly address my research question by comparing the key groups: areas with higher customer counts versus those with lower customer counts. 

<iframe
  src="assets/Customers_vs_len.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

___


# Prediction 

The prediction problem is a multiclass classification task where we aim to predict the cause of the power outage (cause_category). Since the target variable is categorical and has multiple possible categories, this is a multiclass classification problem.

Response Variable
The response variable we're predicting is cause_category, which represents the reason for the power outage (e.g., severe weather, equipment failure, etc.). I chose this variable because understanding the cause of an outage is key for utilities to manage resources, improve

## Baseline Model
- The model I developed uses two features: climate_category (nominal) and outage_len_minutes (quantitative). The climate_category is one-hot encoded, and outage_len_minutes is standardized. I employed a Support Vector Classifier (SVC) to predict the cause_category of power outages. The model achieved an accuracy of 61.25% on the test data, which is better than the baseline model that simply predicts the most frequent class (severe weather) with an accuracy of 50.4%. This indicates that the model is learning useful patterns from the data, but there is still room for improvement.

- While the model performs better than the baseline, the accuracy is moderate, suggesting potential areas for enhancement. Future improvements could include adding more relevant features, hyperparameter tuning, or addressing any class imbalances in the dataset to boost the model’s predictive power

## Final Model

### Added Features
- Customers Affected: This feature provides insight into the scale of the outage and is crucial for predicting the cause of the outage. For instance, large-scale outages might be linked to severe weather events, whereas smaller outages could be due to equipment failure or human error. Including this feature helps the model understand the magnitude of the event, making it more likely to distinguish between different causes based on the affected population.

- Year: This feature captures temporal patterns in the data, such as trends over time in the causes of outages. Certain causes may be more prevalent in specific years due to environmental, technological, or regulatory changes. Adding the year as a feature enables the model to capture such trends.

### Model
chose the Support Vector Classifier (SVC) for the task. SVC is well-suited for classification tasks with relatively small to medium-sized datasets and can effectively handle both linear and non-linear decision boundaries by adjusting its kernel.

### Hyperparameters
1. C: This hyperparameter controls the trade-off between achieving a low error on the training data and maximizing the margin between classes. I tested values of 0.1, 1, and 10, and found that C=1 worked best.
2. Kernel: The kernel determines the type of decision boundary. I tested the "linear", "rbf", and "poly" kernels. The "linear" kernel worked best for this dataset, suggesting that the relationships between features and the target variable are approximately linear.
3. Gamma: This parameter defines how much influence a single training sample has. I tested the values "scale" and "auto", and "scale" performed better.

I used GridSearchCV to perform a grid search over the hyperparameters. This method performs cross-validation on all possible combinations of the hyperparameters and selects the combination that gives the best performance on the validation set.

### Final Model accuracy
The final model, after feature engineering and hyperparameter tuning, achieved an accuracy of **83.3%** on the test data. The improvement is significant, indicating that the added features and the refined model (SVC with the selected hyperparameters) provide much better predictive power.


### Model Fairness
- I am comparing the prediction accuracy of the outage cause category across different climate categories. The groups under consideration are the distinct climate categories, while the evaluation metric is the proportion of correct predictions for each category. The null hypothesis is that there is no significant difference in the prediction accuracy between climate categories, while the alternative hypothesis posits that at least one category shows a significantly different prediction accuracy. To test this, I will use an ANOVA (Analysis of Variance) test, with a significance level of 0.05. The resulting p-value will help determine if the climate category influences the accuracy of predicting the cause of the power outage, guiding whether to reject or fail to reject the null hypothesis.

<iframe
  src="assets/fairness_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
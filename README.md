# Allstate Claims Severity

[The Allstate Corporation](www.allstate.com) is an American-based insurance company, founded in 1931 as part of Sears, Roebuck and Co. and was spun off in 1933. Allstate offers a broad array of protection products through multiple brands and diverse distribution channels, including auto, home, life, and other insurance offered through its Allstate, Esurance, Encompass, SquareTrade, and Answer Financial brands. Allstate is widely known from the slogan "You're in Good Hands with Allstate."

### Objective
- Develop an automated method of predicting the cost, and hence severity, of claims. Model performance is evaluated on the mean absolute error (MAE) between the predicted loss and the actual loss.

### Clients & Impacts:

**Allstate Leadership**: since models developed, once deployed, will be going on autopilot. Accurate loss predictions are crucial as they directly impact the profit line of the company.

**Allstate Claim Department**: automated models will reduce the number of manual claims that need to be calculated by the claim department, which will lead to time save on labor hours and minimize human mistakes on calculation. However, the claim department will still need to monitor and spot checks outputs as control. Inaccurate predictions will results in significant workloads for reworking and should be avoided by all mean possible.

**Allstate Clients**: the whole idea of automated methods is to improve the speed and accuracy of claims payment to over 16 million households being covered by Allstate policy. Client's satisfaction with receiving fair/accurate reimbursement is the ultimate success metric for the models.

### Data Source:
- [Kaggle](https://www.kaggle.com/c/allstate-claims-severity/data)
    - To obtain data, download from the link above, unzip and put all csv files into a folder called "data" OR using the script [HERE](https://github.com/sittingman/allstate_insure/blob/master/0.obtain_data.ipynb)
- Data Dictionary: datasets given had 116 category variables and 14 numeric variables, followed by target variable "loss." Allstate did not provide definitions on the attributes; hence data dictionary is not available.

### Outline of Approach

#### [Data Cleansing/Wrangling](https://github.com/sittingman/allstate_insure/blob/master/1.data_wrangling.ipynb): 
Understanding the shape of the datasets, check for missing value, and invalid records.
- Findings: no missing data. Categories valuables have alphabetical values (i.e., A, B, HK, etc.); continuous variables have values ranging from 0-1. Target variable ('loss') has absolute dollar values

#### [Exploratory Analysis](https://github.com/sittingman/allstate_insure/blob/master/2.exploratory.ipynb): 
Finding variables may correlate with the target variable and eliminate variables that have collinearity. Determine proper transformation based on data structure
- Findings: cont9, cont10, cont12, and cont13 will be dropped as they are highly correlated with existing variables.
- Target variable will require power transformation during model fitting
- Given there are 116 categorical variables, traditional visualization is not effective in identifying features importance. We will apply recursive feature elimination (RFE) and Lasso to narrow down features.
    
#### Machine Learning: 
**Strategy**:
- [Part 1](https://github.com/sittingman/allstate_insure/blob/master/3.ML_p1.ipynb)Identify the best machine learning algorithm based on continuous variables to obtain baseline performance
- [Part 2](https://github.com/sittingman/allstate_insure/blob/master/3.ML_p2.ipynb) Add categorical variables and apply RFE to filter low power features, re-train models, find the best performers
- [Fine tune models](https://github.com/sittingman/allstate_insure/blob/master/4.submit.ipynb) through hyperparameters tuning
    
**Findings:**
- Boosting models perform better than tree base models, which has equivalent performances as linear models. Yet, tree base models have a long processing time. They are not recommended models for this problem.
- The full number of features post one-hot encoding is 1033, of which 123 features were selected after applying LassoCV, RFE, and RFECV dimension reduction methods

**Model performances based on train dataset (test_size =.3, 5-fold cross validation)**

| Model | MAE | Time |
| ---- | ---- | ---- |
|Dummy | 1809 | 1s |
|Linear Reg | 1278 | 3s |
|Ridge | 1278 | 3s|
|SGD | 1278 | 3s|
|Extra Tree | 1249 | 10 mins|
|Random Forecast | 1219 | 23 mins|
|Ada | 1653 | 1 min |
|LightGBM | 1161 | 1 min |
|XGBoost | 1217 | 3 mins |
|CatBoost | 1157 | 19 mins |

We select SGD, Lightbgm, Xgboost, and Catboost to perform hyperparameters tunning.

### Performance Summary

| Model | MAE | Time |
| - | - | - |
|Dummy | 1783 | 1s |
|SGD Regression | 1266 | 4s |
|LightGBM | 1129 | 14s |
|XGBoost | 1149 | 44s |
|CatBoost | 1122| 43s|


### Recommendations/next steps

CatBoost has the best MAE among all models, followed closely by LightGBM.

While the evaluation metric is MAE, clients should be aware it is the average of above 120k insurance claims. MAE understated the impact from outliers to customer satisfaction, particularly for claims payments that are significantly underestimated from the true reimbursement that clients should get. On the other hand, there are low-value claims with high loss predictions. That will result in unnecessary final burdens to Allstate.

The model can be further improved if the definitions of the categorical and continuous variables are given. It will help practitioners to perform more precise features selection and target the outlier issues with a better context. From a high-level, exploratory analysis on the [outliers](https://github.com/sittingman/allstate_insure/blob/master/3.ML-outliers.ipynb), over predictions tend to happen more often at the low-value claims, while under predictions happen toward claims $15K or more in values.

We recommend Allstate to perform audits for claims that have high predicted values (threshold to be determined by Allstate management based on tolerance level). Make it is easy for customers to report inaccurate claims to analyze the miscalculated factors and adjust models accordingly.


[Capstone Report](https://github.com/sittingman/allstate_insure/blob/master/capstone_report_allstate.pdf)

[Presentation Slides](https://github.com/sittingman/allstate_insure/blob/master/allstate_present.pdf)
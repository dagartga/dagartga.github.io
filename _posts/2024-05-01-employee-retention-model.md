---
layout: post
title: Salifort Motors Employee Retention Analysis
image: "/posts/salifort_motors_logo.png"
tags: [XGBoost, Machine Learning, Python, Class Imbalance]
---

In this project, I create a web application that uses an XGBoost model to predict the probability of an employee leaving the company. The model was trained on employee survey data from Salifort Motor Company and provides explanatory results to identify the main driver for why an employee is determined likely to quit. This provides insights that a manager can use to improve the employee's work experience and entice them to stay.

___


# Table of Contents

- [Table of Contents](#table-of-contents)
- [Project Overview and Use Case](#project-overview-and-use-case)
- [Github Repo](#github-repo)
- [Web Application](#web-application)
- [Data](#data)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Feature Engineering](#feature-engineering)
- [Feature Importance](#feature-importance)
- [Model Performance](#model-performance)
  - [Final Model Parameters](#final-model-parameters)
  - [Model Performance Comparison](#model-performance-comparison)
- [Final Model Decision](#final-model-decision)
- [Conclusion](#conclusion)

___

# Project Overview and Use Case <a name="project-overview-and-use-case"></a>

The HR department at Salifort Motors wants to take some initiatives to improve employee satisfaction levels at the company. They collected survey data from employees, but now they don’t know what to do with it. They primarily they would like to answer the following question:

### *What is likely to make the employee leave the company?*

The goal in this project is to analyze the data collected by the HR department and to build a model that predicts whether or not an employee will leave the company.

By being able to predict how likely an employee is to leave the company, Salifort Motors can address the issues of those employees likely to leave. As well, the model can provide import insights into what are the highest drivers of attrition.

Increasing employee retention will improve the company in the following ways:

- No time and energy spent trying to hire a replacement
- No training needed to get a new hire up to speed
- No loss of specialized knowledge
- Keep the built relationships between coworkers


### Use Case

The final model will predict employees that are likely to leave the company. With this prediction, any high risk employees can be flagged and managers can propose changes, such as a promotion or reduced hours, that may entice the employee to stay.


### Ethical Considerations
When dealing with employee data, it is important to remove any demographic data that may create discriminatory bias based on a protected class. As well, it is a good idea to remove any identifiers of individuals. Since this is an experiment, it should not be applied to individuals until fully tested and verified. Looking at the dataset, I do not see any apparent ethical concerns. Personal characteristics such as race, gender, ethnicity, education, and other sensitive information is not included in the dataset.

<br>
<br>

___

# Github Repo <a name="github-repo"></a>
### View the Github Repo Here: [Salifort Motors Repo](https://github.com/dagartga/Salifort_Motors_Employee_Retention_Project)

<br>
<br>

___

# Web Application <a name="web-application"></a>

An interesting experiment is to keep all of the values the same and simply adjust the "Years Spent at Salifort Motors" value. The probability of leaving will increase each of the first 4 years and then start to decline at 5 years and higher. This shows if employees make it to their 5 year work anniversary they are very likely to continue at the company. 

### View the Interactive Web App Here:  [Salifort Motors Project Web App](https://salifort-motors-employee-retention.streamlit.app/)

![Streamlit_App](/img/posts/salifort/salifort_web_app_screenshot.png)

<br>
<br>

___

# Data <a name="data"></a>

Dataset can be downloaded from Kaggle: [Salifort Motors Dataset](https://www.kaggle.com/datasets/leviiiest/salifort-motor-hr-dataset)

| **Variable**              | **Description**                                                        |
|-----------------------|--------------------------------------------------------------------|
| satisfaction_level    | Employee-reported job satisfaction level [0–1]                    |
| last_evaluation       | Score of employee's last performance review [0–1]                  |
| number_project        | Number of projects employee contributes to                         |
| average_monthly_hours| Average number of hours employee worked per month                  |
| time_spend_company    | How long the employee has been with the company (years)            |
| Work_accident         | Whether or not the employee experienced an accident while at work  |
| left                  | Whether or not the employee left the company                       |
| promotion_last_5years | Whether or not the employee was promoted in the last 5 years       |
| Department            | The employee's department                                          |
| salary                | The employee's salary (U.S. dollars)                              |


#### Quick Data Statistics
- 14999 rows
- 11991 unique rows
- 0 missing values

<br>
<br>

___

# Exploratory Data Analysis <a name="exploratory-data-analysis"></a>

### Imbalanced Target

![Employee_Target](/img/posts/salifort/employee_target_pie_chart.png)


#### Adjustments for Imbalanced Target:
1. Use hyperparameter to use weights for balancing
    - **XGBoost:** scale_pos_weight
    - **RandomForest:** class_weight 
2. Stratify data by target on all splits

<br>
<br>

### Pairplot Analysis

![Employee_Pairplot](/img/posts/salifort/pairplot.png)


This is very insightful and by using the hue set to the target, there are some very significant patterns viewable.

-  **Satisfaction Level:**
    - If the satisfaction level is low, then the employee tends to leave.
- **Number of Projects:**
    - If the number of projects is high, then the employee tends to leave.
- **Average Monthly Hours:**
    - If the number of average monthly hours is high, then the employee tends to leave.
- **Time Spend at the Company:**
    - The employees who leave, tend to be 5 for fewer years with a combination of either high number of projects or high number of average monthly hours or very low satisfaction level.

<br>
<br>

### Employee Leaving Cohorts
**When looking at the scatterplot below, there are three cohorts of employees can be seen to leave the company in a higher probability than the average employee.**

![Employees_Leaving](/img/posts/salifort/employee_left_scatterplot.png)

<br>
<br>

### Employee Staying
**When looking at employees who stay, there is fairly evenly distributed scatter among these same characteristics. This signifies these three cohorts are significant.**

![Employees_Staying](/img/posts/salifort/employee_stay_scatterplot.png)

<br>
<br>

### Promotion in the Last 5 Years
**There are very few employees who have gotten a promotion in the last 5 years, as can be seen in the plot below. Perhaps it needs to be adjusted for just employees who have been at the company for 5 years or more to have real significance. Because new employees would not be expected to have a promotion.**

![Employee_Promotion](/img/posts/salifort/employee_promotion_last_5years.png)

<br>
<br>

___

# Feature Engineering <a name="feature-engineering"></a>

- **One of the cohorts with high likelihood of leaving is the group that work >240 monthly hours, have a high evaluation score, but low satisfaction. To better express this relationship between long hours and high evaluation score but low satisfaction, a formula is implemented to turn this relationship into a numeric value.**


### New Features: 
1. **Over Worked High Performer:** (number of projects X number of monthly hours X evaluation score) / satisfaction score
2. **Over 4 Years No Promotion:** if an employee has worked more than 4 years not had a promotion. This eliminates the newer employees who would not be expected to get a promotion.


### Over Worked High Performer Visualization

![over_worked_viz](/img/posts/salifort/overworked_low_satisfaction.png)


### There are three main groups of employees who are likely to leave:

1. **Low Satisfaction/High Performers:**
    - 4-5 years with the company but not promotion
    - Average Monthly Hours greater than 240
    - 6-7 projects
    - High Evaluation Scores
    - Low Satisfaction Scores

- **Recommendation:** These employees are very valuable to the company. They are due for a promotion and likely have too high of a work load, so reduce the work load and/or offer a promotion

2. **Mid Satisfaction/Low Performers:**
    - 3 years with the company
    - Average Monthly Hours less than 165
    - 2-3 projects
    - Low Evaluation Scores
    - Mid Satisfaction Scores

- **Recommendation:** These employees are currently not very valuable to the company. They can take some of the workload from the High  Performers. This could improve retention of the high performers and also possibly increase the retention of this work group. They are possibly bored with the low amount of work. Since this group is not as productive, if they still choose to leave, even after the increase in projects/hours, then they will not be as detrimental. They are typically newer employees and do not drain the company knowledge when they leave.

3. **High Satisfaction/Mid Performers:**
    - Greater than 5 years at the company
    - No Promotion
    - Average Monthly Hours between 215-275
    - 3-5 Projects

- **Recommendation:** These employees are valuable to the company. They have been at the company a long time and likely have a lot of knowledge of company practices. Without a promotion, they are likely to leave. They are not overworked so their workload should not be redistributed. By offering a promotion, their likelihood of leaving will be reduced.

<br>
<br>

___

# Feature Importance <a name="feature-importance"></a>
![model_feature_importance](/img/posts/salifort/xgboost_feat_imp.png)

**Key Takeaways:**
- **Average Monthly Hours**
    - This feature has the **most impact** on retention and if an employee is predicted to leave then the hours of the employee should be viewed to see if they are **over-worked** and then reduce the hours if so.
- **Over Worked High Performer**
    - This feature takes into account the **hours, project, satisfaction, and performance review**. A **high score** means that an employee does **lots of good work** but is **not satisfied**. This assigns value to the employee and if a predicted to leave employee has a high Over Worked High Performer score, they should be aggressively target for retention.
- **Last Evaluation and Satisfaction Level**
    - While these two are expressed in the Over Worked High Performer, this plot shows that they are main contributors to employee leaving. There is a cohort of users who leave who have high satisfaction and a low evaluation score, so they can be identified with this combination of features.
- **Over 4 Years No Promotion**
    -  This feature **does not score high**. Therefor, it may be **less useful to offer a promotion** than to **reduce projects or hours**. But as noticed in the EDA, the distribution of **promotion data is very imbalanced** and **the signal may not strong enough**. Therefor, managers may want to offer the option of either a promotion or a reduced workload. 

<br>
<br>

___

#  Model Performance <a name="model-performance"></a>

Final Model: **Tuned XGBoost Classifier with All Features**


#### Confusion Matrix on Test Set
![confusion_matrix](/img/posts/salifort/best_model_confusion_matrix.png)


**From the Confusion Matrix it can be seen that the Accuracy is 98%**


#### Recall Prioritized Over Precision
For this case, **False Negatives are more important that False Positives**, because **losing an employee means a lot of extra work and down time**. A current employee is already trained and familiar with company best practices. When an employee leaves, there may be days or weeks or longer, where there position is unfilled and HR is looking for a suitable candidate. Thus optimizing for very few False Negatives is more important in this scenario. The cost of a False Positive is that the manager will adjust the workload for the employee and possibly give a promotion. **This should be a lower cost than jarring transition of finding and training a new hire.**


#### Overview of Models Compared
- Random Forest
   - Default
   - Hyperparameters Tuned
   - Hyperparameters Tuned and Features with Department and Promotion are removed 
- XGBoost
   - Default
   - Hyperparameters Tuned
   - Hyperparameters Tuned and Features with Department and Promotion are removed


#### Hyperparameters Tuned
- Random Forest
    - Number of Estimators, Max Features, Min Samples Split, Max Depth, Class Weight
- XGBoost
    - Learning Rate, Number of Estimators, Max Depth, Min Child Weight, Subsample, Scale Positive Weight
  

## Final Model Parameters <a name="final-model-parameters"></a>
- XGBoost Tuned All Features
    - Learning Rate: 0.046
    - Number of Estimators: 276
    - Max Depth: 15 
    - Min Child Weight: 1
    - Subsample: 0.765
    - Scale Positive Weight: 3

<br>
<br>

## Model Performance Comparison <a name="model-performance-comparison"></a>

![model_comparison_recall](/img/posts/salifort/model_comparison_recall.png)

![model_comparison_f1](/img/posts/salifort/model_comparison_f1.png)

![model_comparison_roc_auc](/img/posts/salifort/model_comparison_roc_auc.png)

<br>
<br>

___

# Final Model Decision <a name="final-model-decision"></a>
The **XGBoost Tuned All Features** model was chosen because it had the best Recall score while also having the highest F1 score. This model was trained on all of the provided features as well as the two engineered features, over worked high performer and promotion last 5 years.

- **XGBoost Tuned All Features**
    - Learning Rate: 0.046
    - Number of Estimators: 276
    - Max Depth: 15 
    - Min Child Weight: 1
    - Subsample: 0.765
    - Scale Positive Weight: 3

<br>
<br>


## Project Evaluation <a name="project-evaluation"></a>

- **Final Model Accuracy:**
   -  98% for classifying Employee Churn.

- **Final Model Recall:**
   - 93% of the time the model will correctly predict the employees who will leave.
 
<br>
<br>

___

# Conclusion <a name="conclusion"></a>

The HR department of Salifort Motors conducted the employee survey with the goal of understanding **"What is likely to make an employee leave the company?"**

Upon analysis, there were **four main indicators**. 

The key indicators of attrition are:
- **Average Monthly Hours**
    - According to the feature importance from the predictive model, this feature has the **most impact** on retention and if an employee is predicted to leave then the hours of the employee should be viewed to see if they are **over-worked** and then reduce the hours if so.
- **Combination of Features - (Over Worked High Performer)**
    - An employee with a high score in the "over worked high performer" rating has a high likelihood of leaveing. This feature is a combination of **hours, project, satisfaction, and performance review**. A **high score** means that an employee does **lots of good work** but is **not satisfied**. If a high scoring employee is predicted to leave, then that employee should be aggressively target for retention since they add a lot of value and possibly are unhappy due to too many projects and long hours.
- **Last Evaluation and Satisfaction Level**
    - The importance plot shows that satisfaction level and last evaluation are main contributors to an employee leaving. There is a cohort of users who leave who have high satisfaction and a low evaluation score, so they can be identified with this combination of features.
 

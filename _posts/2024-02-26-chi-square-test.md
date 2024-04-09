---
layout: post
title: Assessing Campaign Performance Using Chi-Square Test For Independence
image: "/posts/ab-testing-title-img.png"
tags: [AB Testing, Hypothesis Testing, Chi-Square, Python]
---

In this project, I  apply Chi-Square Test For Independence (a Hypothesis Test) to assess the performance of two types of mailers that were sent out to promote a new service! 

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results & Discussion](#overview-results)
    - [Github Repo](#github)
- [01. Concept Overview](#concept-overview)
- [02. Data Overview & Preparation](#data-overview)
- [03. Applying Chi-Square Test For Independence](#chi-square-application)
- [04. Analyzing The Results](#chi-square-results)
- [05. Discussion](#discussion)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

Earlier in the year, the client, a grocery retailer, ran a campaign to promote their new "Delivery Club" - an initiative that costs a customer $100 per year for membership, but offers free grocery deliveries rather than the normal cost of $10 per delivery.

For the campaign promoting the club, customers were put randomly into three groups. The first group received a low quality, low cost mailer. The second group received a high quality, high cost mailer. Lastly, the third group were a control group, receiving no mailer at all.

The client knows that customers who were contacted, signed up for the Delivery Club at a far higher rate than the control group, but now wants to understand if there is a significant difference in signup rate between the cheap mailer and the expensive mailer.  This will allow them to make more informed decisions in the future, with the overall aim of optimising campaign ROI!

<br>
<br>
### Actions <a name="overview-actions"></a>

For this test, since focused on comparing the *rates* of two groups, the Chi-Square Test For Independence is the appropriate analysis.  Full details of this test can be found in the dedicated section below.

**Note:** Another option when comparing "rates" is a test known as the *Z-Test For Proportions*.  While, this test could be used, instead I have chosen the Chi-Square Test For Independence because:

* The resulting test statistic for both tests will be the same
* The Chi-Square Test can be represented using 2x2 tables of data - meaning it can be easier to explain to stakeholders
* The Chi-Square Test can extend out to more than 2 groups - meaning the client can have one consistent approach to measuring signficance

From the *campaign_data* table in the client database, we isolated customers that received "Mailer 1" (low cost) and "Mailer 2" (high cost) for this campaign, and excluded customers who were in the control group.

The hypotheses and Acceptance Criteria for the test are as follows:

**Null Hypothesis:** There is no relationship between mailer type and signup rate. They are independent.
**Alternate Hypothesis:** There is a relationship between mailer type and signup rate. They are not independent.
**Acceptance Criteria:** 0.05

As a requirement of the Chi-Square Test For Independence, the data is aggregated down to a 2x2 matrix for *signup_flag* by *mailer_type* and fed into the algorithm (using the *scipy* library) to calculate the Chi-Square Statistic, p-value, Degrees of Freedom, and expected values.

<br>
<br>

### Results & Discussion <a name="overview-results"></a>

Based upon the observed signup rates, there is a difference of 5%:

* Mailer 1 (Low Cost): **32.8%** signup rate
* Mailer 2 (High Cost): **37.8%** signup rate

However, the Chi-Square Test produces the following statistics:

* Chi-Square Statistic: **1.94**
* p-value: **0.16**

The Critical Value for our specified Acceptance Criteria of 0.05 is **3.84**

Based upon these statistics, we fail to reject the null hypothesis. And conclude that there is no relationship between mailer type and signup rate.

In other words - while the higher cost Mailer 2 had a higher signup rate (37.8%) than the lower cost Mailer 1 (32.8%) it appears that this difference is not significant, at least at an Acceptance Criteria of 0.05.

Without running this Hypothesis Test, the client may have concluded that they should always look to go with higher cost mailers - and from what we've seen in this test, that may not be a great decision.  It would result in them spending more, but not *necessarily* gaining any extra revenue as a result

The results here also do not say that there *definitely isn't a difference between the two mailers*, only advising that they should not make any rigid conclusions *at this point*.  

Running more A/B Tests like this, gathering more data, and then re-running this test may provide more insight!

<br>
<br>

### Github Repo <a name="github"></a>

#### View the Github Repo here: [Chi-Squared Github Repo](https://github.com/dagartga/data-science-portfolio) 

<br>
<br>

___

# Concept Overview  <a name="concept-overview"></a>

<br>
#### A/B Testing

An A/B Test can be described as a randomized experiment containing two groups, A and B, that receive different experiences. Within an A/B Test, we look to understand and measure the response of each group - and the information from this helps drive future business decisions.

Application of A/B testing can range from testing different online ad strategies, different email subject lines when contacting customers, or testing the effect of mailing customers a coupon, vs a control group.  Companies like Amazon are running these tests in an almost never-ending cycle, testing new website features on randomized groups of customers...all with the aim of finding what works best so they can stay ahead of their competition.  Reportedly, Netflix will even test different images for the same movie or show, to different segments of their customer base to see if certain images pull more viewers in.

<br>
#### Hypothesis Testing

A Hypothesis Test is used to assess the plausibility, or likelihood of an assumed viewpoint based on sample data, in other words, a it helps us assess whether a certain view we have about some data is likely to be true or not.

There are many different scenarios we can run Hypothesis Tests on, and they all have slightly different techniques and formulas. However they all have some shared, fundamental steps and logic that underpin how they work.

<br>
**The Null Hypothesis**

In any Hypothesis Test, start with the Null Hypothesis. The Null Hypothesis is where the intial viewpoint is stated, and in statistics, and specifically Hypothesis Testing, the initial viewpoint is always that the result is purely by chance or that there is no relationship or association between two outcomes or groups

<br>
**The Alternate Hypothesis**

The aim of the Hypothesis Test is to look for evidence to support or reject the Null Hypothesis.  If we reject the Null Hypothesis, that would mean supporting the Alternate Hypothesis.  The Alternate Hypothesis is essentially the opposite viewpoint to the Null Hypothesis, that the result is *not* by chance, or that there *is* a relationship between two outcomes or groups

<br>
**The Acceptance Criteria**

In a Hypothesis Test, before collecting any data or running any numbers, specify an Acceptance Criteria.  This is a p-value threshold at which to decide to reject or support the null hypothesis.  It is essentially a line drawn in the sand saying "if I was to run this test many many times, what proportion of those times would I want to see different results come out, in order to feel comfortable, or confident that my results are not just some unusual occurrence"

Conventionally, set the Acceptance Criteria to 0.05 - but this does not have to be the case.  For more confidence that something did not occur through chance alone, the p-value can be lowered down to something much smaller. This means that a signifcant difference is found only if the probability of a difference happening by chance is extremely low.

So to summarize, in a Hypothesis Test, the Null Hypothesis is tested using a p-value and then it’s fate is decided based on the Acceptance Criteria.

<br>
**Types Of Hypothesis Test**

There are many different types of Hypothesis Tests, each of which is appropriate for use in differing scenarios - depending on a) the type of data that you’re looking to test and b) the question that you’re asking of that data.

In the case of this task, looking to understand the difference in sign-up *rate* between two groups, using the Chi-Square Test For Independence is a good choice.

<br>
#### Chi-Square Test For Independence

The Chi-Square Test For Independence is a type of Hypothesis Test that assumes observed frequencies for categorical variables will match the expected frequencies.

The *assumption* is the Null Hypothesis, which as discussed above is always the viewpoint that the two groups will be equal.  The Chi-Square Test For Independence will calculate a statistic which, based on the specified Acceptance Criteria will mean either rejecting or supporting this initial assumption.

The *observed frequencies* are the true values seen.

The *expected frequencies* are essentially what would be *expected* to see based on all of the data.

**Note:** Another option when comparing "rates" is a test known as the *Z-Test For Proportions*.  While that test could be used here, the Chi-Square Test For Independence has been chosen because:

* The resulting test statistic for both tests will be the same
* The Chi-Square Test can be represented using 2x2 tables of data - meaning it can be easier to explain to stakeholders
* The Chi-Square Test can extend out to more than 2 groups - meaning the business can have one consistent approach to measuring signficance

___

<br>
# Data Overview & Preparation  <a name="data-overview"></a>

In the client database, there is a *campaign_data* table that shows which customers received each type of "Delivery Club" mailer, which customers were in the control group, and which customers joined the club as a result.

For this task, the goal is to determing if there is enough evidence that the Delivery Club signup rate for customers that received "Mailer 1" (low cost) was different to those who received "Mailer 2" (high cost). Thus from the *campaign_data* table those two groups will be extracted, and the control group will be excluded.

In the code below:

* Load in the Python libraries required for importing the data and performing the chi-square test (using scipy)
* Import the required data from the *campaign_data* table
* Exclude customers in the control group, leaving a dataset with Mailer 1 & Mailer 2 customers only

<br>
```python

# import the required packages
import pandas as pd
from scipy.stats import chi2_contingency, chi2

# import the data
campaign_data = pd.read_excel('./data/grocery_database.xlsx', sheet_name='campaign_data')

# remove control data
campaign_data = campaign_data.loc[campaign_data['mailer_type'] != 'Control']

```
<br>
A sample of this data (the first 10 rows) can be seen below:
<br>
<br>

| **customer_id** | **campaign_name** | **mailer_type** | **signup_flag** |
|---|---|---|---|
| 74 | delivery_club | Mailer1 | 1 |
| 524 | delivery_club | Mailer1 | 1 |
| 607 | delivery_club | Mailer2 | 1 |
| 343 | delivery_club | Mailer1 | 0 |
| 322 | delivery_club | Mailer2 | 1 |
| 115 | delivery_club | Mailer2 | 0 |
| 1 | delivery_club | Mailer2 | 1 |
| 120 | delivery_club | Mailer1 | 1 |
| 52 | delivery_club | Mailer1 | 1 |
| 405 | delivery_club | Mailer1 | 0 |
| 435 | delivery_club | Mailer2 | 0 |

<br>
In the DataFrame we have:

* customer_id
* campaign name
* mailer_type (either Mailer1 or Mailer2)
* signup_flag (either 1 or 0)

___

<br>
# Applying Chi-Square Test For Independence <a name="chi-square-application"></a>

<br>
#### State Hypotheses & Acceptance Criteria For Test

The very first thing needed in any form of Hypothesis Test is to state the Null Hypothesis, the Alternate Hypothesis, and the Acceptance Criteria (more details on these in the section above)

In the code below, these are explcitly and clearly stated so they can be utilized later to explain the results.  As well, the common Acceptance Criteria value of 0.05 is chosen.

```python

# hypothesis stated and acceptance criteria for rejecting the null hypothesis
null_hypothesis = 'There is no relationship between mailer type and signup rate. They are independent.'
alternative_hypothesis = 'There is a relationship between mailer type and signup rate. They are not independent.'
acceptance_criteria = 0.05

```

<br>
#### Calculate Observed Frequencies & Expected Frequencies

As mentioned in the section above, in a Chi-Square Test For Independence, the *observed frequencies* are the true values seen, in other words the actual rates per group in the data itself.  The *expected frequencies* are what would be *expected* based on *all* of the data combined.

The below code:

* Summarizes the dataset to a 2x2 matrix for *signup_flag* by *mailer_type*
* Based on this, calculates the:
    * Chi-Square Statistic
    * p-value
    * Degrees of Freedom
    * Expected Values
* Prints out the Chi-Square Statistic & p-value from the test
* Calculates the Critical Value based upon the selected Acceptance Criteria & the Degrees Of Freedom
* Prints out the Critical Value

```python

# view observed values
observed_values = pd.crosstab(campaign_data['mailer_type'], campaign_data['signup_flag']).values

# calculate the chi-squared statistic
chi2_statistic, p_value, dof, expected_values = chi2_contingency(observed_values, correction=False)
print('Chi-squared Statistic:', chi2_statistic)
>> Chi-squared Statistic: 1.94
print('P-value:', p_value)
>> P-value: 0.16

# find the critical value
critical_value = chi2.ppf(1 - acceptance_criteria, dof)
print('Critical Value:', critical_value)
>> Critical Value: 3.84

```
<br>
Based upon our observed values, here is some context with the sign-up rate of each group:

* Mailer 1 (Low Cost): **32.8%** signup rate
* Mailer 2 (High Cost): **37.8%** signup rate

From this, the higher cost mailer has a higher signup rate by 5%.  The results from our Chi-Square Test will provide the information about how confident that this difference is robust, or if it might have occured by chance.

The resulting Chi-Square Statistic is **1.94** and p-value is **0.16**.  The critical value for the specified Acceptance Criteria of 0.05 is **3.84**

**Note** When applying the Chi-Square Test above, the parameter *correction = False* means that the *Yate's Correction* is applied, which is needed when your Degrees of Freedom is equal to one.  This correction helps to prevent overestimation of statistical signficance in this case.

___

<br>
# Analyzing The Results <a name="chi-square-results"></a>

At this point, everything is in-place to understand the results of the Chi-Square test and from the results above, the resulting p-value of **0.16** is *greater* than the Acceptance Criteria of 0.05, so I will _retain_ the Null Hypothesis and conclude that there is no significant difference between the signup rates of Mailer 1 and Mailer 2.

As well, I make the same conclusion based upon the resulting Chi-Square statistic of **1.94** being _lower_ than the Critical Value of **3.84**

To make this script more dynamic, the code can be written to automatically explain the results in a very thorough way:

```python

# print the p-value results
if p_value <= acceptance_criteria:
    print(f'As the p-value of {p_value} is less than the acceptance criteria of {acceptance_criteria} - we reject the null hypothesis, and conclude that: {alternative_hypothesis}')
else:
    print(f'As the p-value of {p_value} is greater than the acceptance criteria of {acceptance_criteria}  - we fail to reject the null hypothesis, and conclude that: {null_hypothesis}')    

>> As the p-value of 0.16351 is higher than the acceptance_criteria of 0.05 - we fail to reject the null hypothesis, and conclude that: There is no relationship between mailer type and signup rate. They are independent.


# print the chi2 results
if chi2_statistic >= critical_value:
    print(f'As the chi-squared statistic of {chi2_statistic} is greater than the critical value of {critical_value} - we reject the null hypothesis, and conclude that: {alternative_hypothesis}')
else:
    print(f'As the chi-squared statistic of {chi2_statistic} is less than the critical value of {critical_value} - we fail to reject the null hypothesis, and conclude that: {null_hypothesis}')    
    
>> As the chi-square statistic of 1.9414 is less than the critical value of 3.841458820694124 - we fail to reject the null hypothesis, and conclude that: There is no relationship between mailer type and signup rate.  They are independent.

```
<br>
As seen from the outputs of these print statements, we do indeed retain the null hypothesis.  There is not enough evidence that the signup rates for Mailer 1 and Mailer 2 were different and thus there was no significant difference.

___

<br>
# Discussion <a name="discussion"></a>

While the higher cost Mailer 2 had a higher signup rate (37.8%) than the lower cost Mailer 1 (32.8%) it appears that this difference is not significant, at least at the Acceptance Criteria (p-value) of 0.05.

Without running this Hypothesis Test, the client may have concluded that they should always look to go with higher cost mailers and that may not be a great decision.  It would result in them spending more, but not *necessarily* gaining any extra revenue as a result.

The results here also do not say that there *definitely isn't a difference between the two mailers*, only that the client shouldn't not make any rigid conclusions *at this point*.  

Running more A/B Tests like this, gathering more data, and then re-running this test may provide more insight!
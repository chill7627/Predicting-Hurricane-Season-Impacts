## Statistical Testing of Hypotheses for the United States Gulf Coast Hurricane Data

After the initial exploratory data analysis, some correlations and relations among the data seemed visually evident.  Here are the statistical tests for those relations and correlations.

### Initial Hypotheses

1. Correlation of number of hurricanes to any of the World Ocean Database features.
2. Correlation of average yearly strength of hurricanes to any of the World Ocean Database features.
3. Coast length correlates with overall number of hurricanes per state.

***

### 1. Correlation of number of hurricanes to any of the World Ocean Database features.

#### Test Setup

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

* Null hypothesis is that the data aren't correlated.
* Create permutation replicates of the wod feature data while holding the hurricane data constant.
* Test statistic is the correlation coefficient.
* p-value is the sum of the correlations at least as extreme as the empirical correlation.
* alpha = 0.05 significance level.

#### Results

|WOD Feature|Empirical Correlation|p-value|Significant Result|
|-----------|---------------------|-------|------------------|
|Oxygen     |-0.139|0.156|No|
|Phosphate|0.161|0.106|No|
|Salinity|-0.215|0.075|No|
|Silicate|0.122|0.156|No|
|Temperature|-0.363|0.006|Yes|

The only statistically significant correlation with $\alpha$ = .05 significance level is between temperature and the number of hurricanes.

***

### 2. Correlation of average yearly strength of hurricanes to any of the World Ocean Database features.

#### Test Setup

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

* Null hypothesis is that the data aren't correlated.
* Create permutation replicates of the wod feature data while holding the strength data constant.
* Test statistic is the correlation coefficient.
* p-value is the sum of the correlations at least as extreme as the empirical correlation.
* alpha = 0.05 significance level.

#### Results

|WOD Feature|Empirical Correlation|p-value|Significant Result|
|-----------|---------------------|-------|------------------|
|Oxygen     |-0.243|0.038|Yes|
|Phosphate|0.033|0.382|No|
|Salinity|0.052|0.355|No|
|Silicate|0.133|0.174|No|
|Temperature|-0.204|0.070|No|

The only statistically significant correlation with alpha = .05 significance level is between oxygen and the average yearly strength.

***

### 3. Coast length correlates with overall number of hurricanes per state.

#### Test Setup

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

* Null hypothesis is that the data aren't correlated.
* Create permutation replicates of the coast length data while holding the number of hurricanes data constant.
* Test statistic is the correlation coefficient.
* p-value is the sum of the correlations at least as extreme as the empirical correlation.
* alpha = 0.05 significance level.

#### Results

|Empirical Correlation|p-value|Significant Result|
|---------------------|-------|------------------|
|0.940|0.009|Yes|

The result is statistically significant with alpha = .05 significance level.  Moreover, the fact that the empirical correlation is very high suggests that coast length has strong influence on the number of hurricanes that impact a particular state along th gulf coast.

***

### Additional Hypotheses

* After reviewing the results of the previous test a couple more can be tested to solidify a statistical answer.
* Since temperature and the number of hurricanes were negatively correlated, and oxygen and average strength were negatively correlated:
    * A. Colder yearly average ocean temperatures lead to higher number of hurricanes.
    * B. Lower yearly average ocean oxygen content leads to higher average yearly strength of hurricanes.

#### A. Colder yearly average ocean temperatures lead to higher number of hurricanes.

#### Test Setup

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

* Try different group splitting temperature.
* Null hypothesis is that there isn't a difference in means of the two groups.
* Shift high and low temperature data groups to have the same mean.
* Create bootstrap replicates of both groups.
* The test statistic is difference of means (low temperature mean - high temperature mean).
* p-value is the sum of the replicates that have a difference of means at least as extreme as the empirical difference of means.
* alpha = 0.05 significance level.

#### Results

|Divide Temperature|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|21 C|3.558|0.000|Yes|1|
|22 C|3.235|0.245|No|4|
|23 C|1.553|0.222|No|7|
|24 C|0.854|0.256|No|13|
|25 C|1.156|0.175|No|25|
|26 C|1.183|0.178|No|17|
|27 C|0.894|0.279|No|8|

The only result with significance is when the separation temperature is 21 C.  The data is skewed beyond divide temperatures of 25 and 26 because of one group having very little data points.  No statistical inferences can be made from the tests.  More data may help to give significant results.

A t-test was run.  However the data may not exactly meet the conditions of the t-test, so the results must be taken with a grain of salt.  I would say more data points would aid in a more significant conclusion.  Here are the results of the t-test.

|Divide Temperature|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|21 C|3.558|N/A|No|1|
|22 C|3.235|0.265|No|4|
|23 C|1.553|0.339|No|7|
|24 C|0.854|0.335|No|13|
|25 C|1.156|0.035|Yes|25|
|26 C|1.183|0.009|Yes|17|
|27 C|0.894|0.099|No|8|

After trying t-tests, the results are similar with significance at the extreme temperature range separations.  So, temperatures less than 21 and greater than 25 as the divding points give significant results (p-value < 0.05).  I would say more data is needed before making any conclusions.  The low and high data group respectively have very few data points.

It still stands that there is a statistically significant negative correlation between the temperature and number of hurricanes.

A good dividing temperature that would signify a statistically higher chance of hurricanes is hard to discern with the limited amount of data.

***

### B. Lower yearly average ocean oxygen content leads to higher average yearly strength of hurricanes.

#### Test Setup

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

* Try different group splitting oxygen content values.
* Null hypothesis is that there isn't a difference in means of the two groups.
* Shift high and low oxygen content data groups to have the same mean.
* Create bootstrap replicates of both groups.
* The test statistic is difference of means(low content mean minus high content mean).
* p-value is the sum of the replicates that have a difference of means at least as extreme as the empirical difference of means.
* alpha = 0.05 significance level.

#### Results

|Divide Oxygen Content|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|4.384|-1.065|0.190|No|3|
|4.885|0.721|0.358|No|26|
|5.385|0.762|0.309|No|13|

None of the oxygen level group tests are significant.  I believe this once again suffers from too few data points.

A t-test was run.  However the data may not exactly meet the conditions of the t-test, so the results must be taken with a grain of salt.  I would say more data points would aid in a more significant conclusion.  Here are the results of the t-test.

|Divide Oxygen Content|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|4.384|-1.065|0.061|No|3|
|4.885|0.721|0.031|Yes|26|
|5.385|0.762|0.035|Yes|13|

The t-tests show significant results if the median of the oxygen content is used as the group divider suggesting that there might be a chance of more hurricanes if the oxygen content in the gulf is low.

However the data may not exactly meet the conditions of the t-test, so the results must be taken with a grain of salt.  I would say more data points would aid in a more significant conclusion.

However, a significant negative correlation does exist between the yearly average oxygen content of the gulf and the average strength of the hurricanes.

### Summary
There is a significant negative correlation between the yearly average gulf temperature and the yearly number of hurricanes affecting the gulf coast.

Also, there is a significant negative correlation between the yearly average oxygen content of the gulf and the yearly average strength of the hurricanes that affect the gulf coast.

No significant dividing temperature or oxygen content could be found with available data to give a boundary for higher chance of hurricanes and/or higher strengths for the year.

More data is needed to provide a larger sample size and more statistical power.

The coast length of each state is a huge determining factor in the number of hurricanes that will hit particular states in a year.

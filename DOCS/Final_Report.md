# Predicting Hurricane Season Impacts
  **Milestone Report**
  
  **28th April 2018**
  
  **Christopher Seth Hill**

****

## OVERVIEW

Every year the Gulf Coast is impacted by numerous tropical weather systems.  The deadliest and most damaging of these being hurricanes.  A single hurricane can cause damages of $50 billion plus and rack up a hefty death toll.  Currently, Gulf Coast states could use a simple way to assess or forecast potential landfalls for upcoming hurricane seasons.  Having a predictive model for hurricane season impacts as landfalls would aid states in allocating resources and budgeting for probable damages.

****

## INTERESTED PARTIES

The federal, state, and local governments, especially those along the Gulf Coast, would be interested in a way to predict the impact of upcoming hurricane seasons.  This would allow the governments and citizens to better gauge the necessity for hurricane damage mitigating resources.  More importantly, this would also help in decreasing the loss of life associated with hurricanes.  In addition, this would also aid in budgeting, preventing over or under budgeting.

****

## DATA

### Data Wrangling World Ocean and Historical Hurricane Data

**1. National Oceanic and Atmospheric Administration World Ocean Database**

* **Background Information and File Selection**
    
The World Ocean Database (WOD) is a massive data collection and storage project supported by NOAA.  It houses copious amounts of  data from the world's oceans.  Data is stored in datasets based on the various instruments used to collect the data.  The data contains depth profile information about ocean properties such as: 
* Temperature
* Salinity
* pH
* Pressure
* Phosphates
* Silicates
* And more that weren't used because of scarcity in the OSD data.
			
The methods of Data Acquisition employed to gather the data are outlined in the *Table 1* below:

| Dataset | Source                                                                                                 |
|:--------|:-------                                                                                                |
|OSD      |Bottle, low-resolution Conductivity-Temperature-Depth (CTD), low-resolution XCTD data, and plankton data|
|CTD      |High-resolution Conductivity-Temperature-Depth (CTD) data and high-resolution XCTD data                 |
|MBT      |Mechanical Bathythermograph (MBT) data, DBT, micro-BT                                                   |
|XBT      |Expendable (XBT) data                                                                                   |
|SUR      |Surface only data (bucket, thermosalinograph)                                                           |
|APB      |Autonomous Pinniped Bathythermograph - Time-Temperature-Depth recorders attached to elephant seals      |
|MRB      |Moored buoy data from TAO (Tropical Atmosphere-Ocean), PIRATA (moored array in the tropical Atlantic), MARNET, and TRITON (Japan-JAMSTEC)|
|PFL      |Profiling float data                                                                                    |
|DRB      |Drifting buoy data from surface drifting buoys with thermistor chains                                   |
|UOR      |Undulating Oceanographic Recorder data from a Conductivity/Temperature/Depth probe mounted on a towed undulating vehicle|
|GLD      |Glider data                                                                                             |

*Table 1: Lists the different instruments used by NOAA to collect the datasets along with descriptions of the different instruments.*

Data is organized in files that I selected based on geographical location.  I chose only the areas relevant for the Gulf of Mexico.  The locations were based on 10&deg; grid blocks.  The selection map is presented below in *Figure 1*:

![alt text](https://www.nodc.noaa.gov/OC5/WOD/wmo_world13.png "Sectioned World Map")

*Figure 1: Image of the WOD file selection tool based on geographical locations.*

I selected 7208 and 7209 locations from *Figure 1*.  The next step was to determine which instrument data that I wanted to use.  I landed on the OSD data as it seemed to have the most properties in one dataset.  More detailed information about the data and how it is organized can be found in the [WOD Documentation](https://www.nodc.noaa.gov/OC5/WOD/docwod.html).  In future steps more data could be used from the other instruments by merging this data into the data already wrangled.

* **File Download and Incorpation into Pandas DataFrames**

The files are zipped and in a proprietary ascii format.  The files contain multiple WOD profiles that each have depth profile data at a specific latitude, longitude and timestamp.  I used the [wodpy package](https://pypi.python.org/pypi/wodpy/1.5.0) which was created to decode the profile data contained in the WOD files.  I used wodpy to create dataframes from the profiles.  The resulting dataframes contain the salinity, temperature, etc data in columns with standard depths being the index, and the location and time is stored as attributes for that particular dataframe.  Moreover, I iterated over each profile in the file and stored the resulting dataframes in a list.  I then created an empty master dataframe and appended the dataframes in the list to the master dataframe.

In examining the dataframes being appended to the master, I noticed there were empty dataframes.  I chose to check to see if a dataframe was empty before appending to the master in order to filter out the empty dataframes.

* **Exploring, Manipulating, and Cleaning**
    
Now that I had one master data frame with the relevant data, I could begin to clean, organize, and manipulate the data.  Firstly, the time information was separated into multiple columns for month, day, year, and time.  I created one column with a datetime object and sorted the dataframe based on these values.  I also dropped the redundant columns from the dataframe.

Next, the latitude and longitude data were in separate columns.  I zipped them together into tuples in one column to represent a combined (lat, lon) location.  I then dropped the redundant longitude and latitude columns from the dataframe.

Then, I made a decision to drop all data predating 1960 as this was when the first satellite was launched to track weather http://www.aoml.noaa.gov/hrd/tcfaq/J6.html.

After looking at the latitude and longitude data more closely, it was inconsistent with rounding and decimal places.  I chose to round these values to the nearest degree.  I then created a multiIndex for the dataframe based on location and date.  Next, I resampled each location by monthly averages.  

Upon inspection, the data was mostly missing for the pH and pressure columns, so I chose to drop them from the dataframe.  Moreover, I filled NaN values by first filling NaNs by location averages.  Then, the remaining NaNs were filled by back filling.  Below in *Figure 2* is the head of the created dataframe.  After these wrangling steps I did some EDA into how many data points there were per location in the dataframe just created.  The summary graph of this information is shown in *Figure 3* below:

<img width="700" alt="wod_loc_df_head" src="https://user-images.githubusercontent.com/23604099/39396940-1fffed6c-4ac5-11e8-90df-7e97b491adc7.PNG">

*Figure2: Head of the resulting wrangled dataframe indexed by location and date.*

![wod data histogram](https://user-images.githubusercontent.com/23604099/36941433-162e8176-1f29-11e8-8cdc-01042197e9b0.png)

*Figure 3: The number of datapoints per location is on the x axis, and the height of the bars indicate the number of locations in the dataframe index with that many datapoints.*

After looking at the number of datapoints per location analysis, it was evident that most of the locations had very few datapoints.  Resultingly, I decided to make another dataframe for the WOD data based on time as the only index.  I did this by dropping the location level of the index and sorting by the sole remaining time index.  Next, I performed EDA on this time indexed dataframe.

After performing EDA on the individual features of the dataframe, I decided to leave the high data points in the dataframe as they seemed they could be real.  The following *Figure 4* and *Figure 5* illustrate the overall data:

![boxplots wod data](https://user-images.githubusercontent.com/23604099/36941472-012e9ba2-1f2a-11e8-9db3-f71236c21324.png)

*Figure 4: Boxplots of the WOD features standardized values.*

![line plots wod data](https://user-images.githubusercontent.com/23604099/36941476-0c368f50-1f2a-11e8-955c-8471e35ed4c7.png)

*Figure 5: Time series plot of the standardized wod data features.*

**2. Historical Hurricane Data**
  
* **Background Information and File Selection**
    
The Historical Hurricane Data lists the hurricanes per year,  their categories, and their landfall locations.  I found this data in wiki tables separated by state in the [List of United States Hurricanes](https://en.wikipedia.org/wiki/List_of_United_States_hurricanes) wiki page.  

In order to wrangle the data into python, I had to scrape the wiki page's tables.  I used the [wikipedia package](https://pypi.python.org/pypi/wikipedia).  Below in *Figure 6* is an example of a dataframe straight from the wiki table:

<img width="700" alt="sample raw wiki table" src="https://user-images.githubusercontent.com/23604099/36947617-b9c71e2e-1f9c-11e8-8c30-022f90e0aa77.PNG">

*Figure 6: Sample of the raw dataframes scraped from the wiki tables.* 

The tables from the wiki page have several issues that need to be cleaned up.  First I fixed the fact the tables were set up in a split table fashion.  I had to put the data into one long dataframe.  Also, the date was split into two columns.  I fixed this by using datetime.strptime() method, which parsed the dates in the separate columns.  In addition, I removed any blank rows that existed in the dataframes.  The result is below in *Figure 7*: 

<img width="700" alt="post datetime and concat hurricane data" src="https://user-images.githubusercontent.com/23604099/36947997-ed6d0f36-1fa1-11e8-8568-52fe0d232b13.PNG">

*Figure 7: Resulting dataframe after first wrangling steps outlined above.*

Next steps included:
* Removing any special [notes] in the category column.
* Renaming the remaining columns.
* Replacing special entries in quotations in the named column with "Unnamed".
* Adding a corresponding state label for each dataframe entry.
* Removing any entries pre-dating 1960 from the dataframe.

The result is below in *Figure 8*:

<img width="700" alt="final hurricane df" src="https://user-images.githubusercontent.com/23604099/37010989-15f23f7e-20bc-11e8-8c18-3d3e6a8ec072.PNG">

*Figure 8: The final wrangled dataframe containing the historical list of hurricanes to impact the United States Gulf Coast.*

**3. Additional Datasets**

Additional data could be incorporated for a more inclusive picture of the Gulf of Mexico.  By utilizing the other data probes in the World Ocean Database, a more filled dataset for gulf features could be created.  In addition, more of the World Ocean Database data could be used to gather information on how a larger area of the earth's oceans impact hurricanes and tropical weather worldwide.

More data could be taken from more global weather patterns such as the ones utilized by NOAA to predict hurricane activity (landfalls are not predicted) for upcoming seasons.  These are outlined below:

1. The conducive conditions which were predicted in May for the Atlantic hurricane Main Development Region (MDR, which includes the tropical Atlantic and Caribbean Sea) are now present and are expected to persist through the peak months (August-October, ASO) of the hurricane season. These conditions include weaker vertical wind shear, weaker trade winds, more conducive wind patterns coming off of Africa, and a stronger west African monsoon.

2. There is much higher confidence that ENSO-neutral conditions will persist through ASO, and that El Niño will not develop and suppress the season. 

3. Warmer sea surface temperatures (SSTs) in the MDR are more likely to persist than key models had previously predicted.

4. All models now predict a more active season than they did in May.

5. Enhanced June-July activity in the tropical Atlantic (tropical storms Bret and Don) reinforces the expectation for an above-normal season. Historically, years with early-season activity in this region have a higher likelihood of being above-normal.

Moreover, damage and casualty information could be gathered in an effort to predict damage amounts for upcoming seasons.

****

### INITIAL DATA EXPLORATION

**Questions**

In exploring the datasets created in the Data Wrangling step, I wanted to look into answering three main questions.
1. I wanted to get a feel for the impacts that hurricanes have had over the years on the Gulf Coast of the United States.  This includes frequency, strength, and strength frequency for both the Gulf Coast as a whole and per state.
2. Are there any factors of the ocean properties that trend well with the frequency of hurricanes?
3. Take a look into the data broken down by state.

**Answers**

**1. Hurricane Impacts**

* A trend of 0-3 hurricanes per year is pretty steady throughout history with a few outlying years, most notably 2005 with 11 hurricane impacts along the gulf coast.  Zero hurricanes is the most frequent occurence throughout history. 90% of the years had 3 hurricanes or less with most years having 0 or 1 hurricanes per year.  These points are illustrated below in *Figure 9*, *Figure 10*, and *Figure 11*:

<img width="700" alt="hurricanes per year over time" src="https://user-images.githubusercontent.com/23604099/38057950-1c57f62e-32af-11e8-97ae-19672a6378ae.PNG">

*Figure 9: Time series of the number of hurricanes per year that impacted the US gulf coast.*

<img width="700" alt="frequency of hurricane occurences per year" src="https://user-images.githubusercontent.com/23604099/38058000-4e885300-32af-11e8-8a53-886ecd1f10c0.PNG">

*Figure 10: Frequency of occurences of hurricanes per year.  The x axis is number of hurricanes per year.  The y axis is the amount of years since 1960 that had that number of hurricanes per year.*

<img width="700" alt="cdf hurricanes per year" src="https://user-images.githubusercontent.com/23604099/38058034-6e2fee0c-32af-11e8-81fa-ea6330a7d25f.PNG">

*Figure 11: Cummulative Distribution Function plot for the number of hurricanes per year.  The 90% mark is annotated.*

* After reviewing the category data, it shows that the most frequent category is Category 1 and the least frequent is Category 5. Moreover, the most frequent occurence is for the Gulf Coast to have one Category 1 hurricane per year.  Hurricanes are ranked in strength with the weakest being Category 1  and the strongest being Category 5.  These points are illustrated in *Figure 12* and *Figure 13* below:

<img width="700" alt="frequency of categories per year" src="https://user-images.githubusercontent.com/23604099/38058464-ebd5b6b0-32b0-11e8-9bc4-da7db2552441.PNG">

*Figure 12: Category counts for all time since 1960.*

<img width="700" alt="frequency of category occurences per year" src="https://user-images.githubusercontent.com/23604099/38058656-a09557f4-32b1-11e8-8e9d-ee63d0f528cf.PNG">

*Figure 13: Category Occurence Frequencies.  The categories are separated into their own plots.  The x-axis is the number of occurences of that particular category per year.  The y-axis is the number of years that had those occurences per year.*

**2. Ocean Properties and Hurricane Impacts Trends**

I looked at the ocean data of the Gulf of Mexico averaged as a whole.  I investigated whether there are any correlations between the ocean properties and the number of hurricanes that impacted the gulf coast.  Also, I looked into the correlations between ocean features and average strengths of the storms.

* A time series analysis of ocean features revealed that most of the features have remained relatively steady besides temperature. There have been upsets in all the factors. Most interesting was that the average temperature for the gulf was much lower the year of 2005, the year of 11 hurricane impacts.  Dips in the temperature can be observed for every significant peak in the number of hurricanes.  All the features have low correlations with the ocean data with temperature being the strongest.  Also, there is a fair amount of positive correlation between salinity and temperature, and silicates and oxygen. There seems to also be some negative correlation between silicates and temperature and salinity and oxygen, and phosphates and salinity.

* Below in *Figure 14* are line plots of the features averaged over the entire Gulf of Mexico per year vs. the hurricanes per year.

<img width="700" alt="ocean_params_time" src="https://user-images.githubusercontent.com/23604099/38146476-28fe6f66-341c-11e8-9f37-52a28a5c4e3d.png">

*Figure 14: Time series analyses of the WOD features shown in comparison to the number of hurricanes per year.

* Next are Pearson correlation coefficients for each ocean feature and the number of hurricanes per year shown in *Table 2*.

|Ocean Parameters|Correlations with Number of Hurricanes per Year|
|----------------|-----------------------------------------------|
|Oxygen|-0.139101|
|Phosphate|0.161184|
|Salinity|-0.214846|
|Silicate|0.122182|
|Temperature|-0.362684|

*Table 2: WOD parameters and their correlations with the number of hurricanes per year.*

**3. Data Analyzed by State**

After looking at the gulf of Mexico as a whole, I thought it would be worthwhile to look into the breakdowns by state.

* After review of cursory hurricanes by state data, Florida and Louisiana are impacted the most with Alabama and Mississippi impacted the least. Florida is impacted by higher strength storms compared to the rest of the states, which are mostly affected by category 1 storms.  Moreover, the highest likelihood is to be impacted by 0 or 1 storms per year.  These points are illustrated below in *Figure 15*, *Figure 16*, and *Figure 17*.

<img width="700" alt="hurricane state count" src="https://user-images.githubusercontent.com/23604099/38064628-fe578c8e-32cc-11e8-8443-92d3ea32feb5.PNG">

*Figure 15: Total hurricane count since 1960 for each state on the US gulf coast.*

<img width="700" alt="categories by state" src="https://user-images.githubusercontent.com/23604099/38064675-4c6a6612-32cd-11e8-93e2-710679244217.PNG">

*Figure 16: Total number of category count since 1960 for each state on the US gulf coast.*

<img width="700" alt="hurricanes per year by state" src="https://user-images.githubusercontent.com/23604099/38144432-aed1b350-3412-11e8-99bb-3cd8ccfc20ed.PNG">

*Figure 17: Time series analysis of number hurricanes per year categorized by state.*

* After answering the above simple initial questions, the data seemed to show that certain states were always being impacted more frequent and with stronger storms.  I decided to dig in deeper into different features of the states to see if they showed any interesting trends and or correlations.

* After looking into hurricane counts vs. coast length for each state it seems that there is a positive correlation between the two quantities. This suggest that states with larger coast lines tend to be impacted more often than states with smaller coast lines.  This is illustrated in *Figure 18*.  This may just be circumstantial to other factors such as sea temperature, sea floor depth, etc. along those coast lines.

<img width="700" alt="coast length vs hurricanes" src="https://user-images.githubusercontent.com/23604099/38143248-a4f37fd0-340d-11e8-8012-79d02c56344e.PNG">

*Figure 18: Scatter plot of coast length vs. total number of hurricanes for each state.  Linear regression line fitted.*

* Next heatmaps were created of the gulf of mexico and compared with the number of hurricanes broken down by category per year.  The heatmaps of the parameters by year and the hurricane counts per year seems to visually show that there is some correlation between the wod data and the number of hurricanes per year, mainly a negative correlation between temperature and the number of hurricanes per year.  These points are illustrated in *Figure 19*, *Figure 20*, *Figure 21*, and *Figure 22*.

<img width="700" alt="interact heatmap 2005 temp" src="https://user-images.githubusercontent.com/23604099/38428698-69ac7cba-398a-11e8-8276-a48254d6e34f.PNG">

*Figure 19: Heatmap of Gulf of Mexico temperature for the year 2005.  Blue represents colder temperatures and red represents warmer temperatures.* 

<img width="700" alt="interact bar plot temp 2005" src="https://user-images.githubusercontent.com/23604099/38428718-78b210b2-398a-11e8-9038-cfe114f686b7.PNG">

*Figure 20: Number of hurricanes separated by category for each state in the year 2005.*

<img width="700" alt="interact heatmap 2007 temp" src="https://user-images.githubusercontent.com/23604099/38959214-c7ae02a0-432d-11e8-81d6-f152019af9ad.PNG">

*Figure 21: Heatmap of Gulf of Mexico temperature for the year 2007.  Blue represents colder temperatures and red represents warmer temperatures.* 

<img width="700" alt="interact bar plot temp 2007" src="https://user-images.githubusercontent.com/23604099/38959224-cce8b58a-432d-11e8-929e-db3e12327572.PNG">

*Figure 22: Number of hurricanes separated by category for each state in the year 2007.*

**Initial Data Exploration Summary**

The number of hurricanes that affect the Gulf Coast of the United States each year is normally 3 or less.  The intensities of these storms are usually of the weakest variety being category 2 or less.  On a state by state basis, Florida and Louisiana get impacted the most often with Alabama and Mississippi being the least impacted.  There initially seems to be some correlation between the ocean parameters and the number of hurricanes that impact the gulf coast.  Most notably would be a negative correlation between the temperature and the number of hurricanes that impact the gulf coast.  The strongest correlation that was initially explored is a strong positive correlation between the length of each states gulf coast and the historical number of hurricanes that impacted that state.  This makes sense if you think of it as a bucket catching rain drops.  A bigger bucket will stastically fill up faster. 

****

## Statistical Testing Results

After the initial exploratory data analysis, some correlations and relations among the data seemed visually evident.  Here are the statistical tests for those relations and correlations.

### Initial Hypotheses

1. Correlation of number of hurricanes to any of the World Ocean Database features.
2. Correlation of average yearly strength of hurricanes to any of the World Ocean Database features.
3. Coast length correlates with overall number of hurricanes per state.

**1. Correlation of number of hurricanes to any of the World Ocean Database features**

**Test Setup**

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of a test, hacker statistics can test it.  Below are the steps used in this test:

   * Null hypothesis is that the data aren't correlated.
   * Create permutation replicates of the wod feature data while holding the hurricane data constant.
   * Test statistic is the correlation coefficient.
   * p-value is the sum of the correlations at least as extreme as the empirical correlation.
   * alpha = 0.05 significance level.

**Results**

|WOD Feature|Empirical Correlation|p-value|Significant Result|
|-----------|---------------------|-------|------------------|
|Oxygen     |-0.139|0.156|No|
|Phosphate|0.161|0.106|No|
|Salinity|-0.215|0.075|No|
|Silicate|0.122|0.156|No|
|Temperature|-0.363|0.006|Yes|

*Table 3: Results of the test for correlation between WOD ocean parameters and the number of hurricanes per year.*

The only statistically significant correlation with $\alpha$ = .05 significance level is between temperature and the number of hurricanes.

**2. Correlation of average yearly strength of hurricanes to any of the World Ocean Database features**

**Test Setup**

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

   * Null hypothesis is that the data aren't correlated.
   * Create permutation replicates of the wod feature data while holding the strength data constant.
   * Test statistic is the correlation coefficient.
   * p-value is the sum of the correlations at least as extreme as the empirical correlation.
   * alpha = 0.05 significance level.

**Results**

|WOD Feature|Empirical Correlation|p-value|Significant Result|
|-----------|---------------------|-------|------------------|
|Oxygen     |-0.243|0.038|Yes|
|Phosphate|0.033|0.382|No|
|Salinity|0.052|0.355|No|
|Silicate|0.133|0.174|No|
|Temperature|-0.204|0.070|No|

*Table 4: Results of the test for correlation between WOD ocean parameters and the average strength of hurricanes per year.*

The only statistically significant correlation with alpha = .05 significance level is between oxygen and the average yearly strength.

**3. Coast length correlates with overall number of hurricanes per state.**

**Test Setup**

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

   * Null hypothesis is that the data aren't correlated.
   * Create permutation replicates of the coast length data while holding the number of hurricanes data constant.
   * Test statistic is the correlation coefficient.
   * p-value is the sum of the correlations at least as extreme as the empirical correlation.
   * alpha = 0.05 significance level.

**Results**

|Empirical Correlation|p-value|Significant Result|
|---------------------|-------|------------------|
|0.940|0.009|Yes|

*Table 5: Results of the test for correlation state coastline length and the number of hurricanes per year.*

The result is statistically significant with alpha = .05 significance level.  Moreover, the fact that the empirical correlation is very high suggests that coast length has strong influence on the number of hurricanes that impact a particular state along th gulf coast.

### Additional Hypotheses

* After reviewing the results of the previous test a couple more hypotheses can be tested to solidify a statistical answer.
* Since temperature and the number of hurricanes were negatively correlated, and oxygen and average strength were negatively correlated:
    * A. Colder yearly average ocean temperatures lead to higher number of hurricanes.
    * B. Lower yearly average ocean oxygen content leads to higher average yearly strength of hurricanes.

**A. Colder yearly average ocean temperatures lead to higher number of hurricanes**

**Test Setup**

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

   * Try different group splitting temperature.
   * Null hypothesis is that there isn't a difference in means of the two groups.
   * Shift high and low temperature data groups to have the same mean.
   * Create bootstrap replicates of both groups.
   * The test statistic is difference of means (low temperature mean - high temperature mean).
   * p-value is the sum of the replicates that have a difference of means at least as extreme as the empirical difference of means.
   * alpha = 0.05 significance level.

**Results**

|Divide Temperature|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|21 C|3.558|0.000|Yes|1|
|22 C|3.235|0.245|No|4|
|23 C|1.553|0.222|No|7|
|24 C|0.854|0.256|No|13|
|25 C|1.156|0.175|No|25|
|26 C|1.183|0.178|No|17|
|27 C|0.894|0.279|No|8|

*Table 6: Results of the test that colder yearly average gulf temperatures lead to higher number of hurricanes per year.*

The only result with significance is when the separation temperature is 21 C.  The data is skewed beyond divide temperatures of 25 and 26 because of one group having very few data points.  No statistical inferences can be made from the tests.  More data may help to give significant results.

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

*Table 7: T-test results of the test that colder yearly average gulf temperatures lead to higher number of hurricanes per year.*

After trying t-tests, the results are similar with significance at the extreme temperature range separations.  So, temperatures less than 21 and greater than 25 as the divding points give significant results (p-value < 0.05).  I would say more data is needed before making any conclusions.  The low and high data group respectively have very few data points.

It still stands that there is a statistically significant negative correlation between the temperature and number of hurricanes.

A good dividing temperature that would signify a statistically higher chance of hurricanes is hard to discern with the limited amount of data.

**B. Lower yearly average ocean oxygen content leads to higher average yearly strength of hurricanes**

**Test Setup**

I chose to use hacker statistics as they are more widely applicable and can benefit from numerous simulated samples.  With hacker statistics if you can think of test, hacker statistics can test it.  Below are the steps used in this test:

   * Try different group splitting oxygen content values.
   * Null hypothesis is that there isn't a difference in means of the two groups.
   * Shift high and low oxygen content data groups to have the same mean.
   * Create bootstrap replicates of both groups.
   * The test statistic is difference of means(low content mean minus high content mean).
   * p-value is the sum of the replicates that have a difference of means at least as extreme as the empirical difference of means.
   * alpha = 0.05 significance level.

**Results**

|Divide Oxygen Content|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|4.384|-1.065|0.190|No|3|
|4.885|0.721|0.358|No|26|
|5.385|0.762|0.309|No|13|

*Table 8: Results of the test that lower yearly average gulf oxygen content leads to higher yearly strength average of hurricanes.*

None of the oxygen level group tests are significant.  I believe this once again suffers from too few data points.

A t-test was run.  However the data may not exactly meet the conditions of the t-test, so the results must be taken with a grain of salt.  I would say more data points would aid in a more significant conclusion.  Here are the results of the t-test.

|Divide Oxygen Content|Empirical Difference of Means|p-value|Significant Result|Lowest Group Size|
|------------------|-----------------------------|-------|------------------|-----------------|
|4.384|-1.065|0.061|No|3|
|4.885|0.721|0.031|Yes|26|
|5.385|0.762|0.035|Yes|13|

*Table 9: T-test results of the test that lower yearly average gulf oxygen content leads to higher yearly strength average of hurricanes.*

The t-tests show significant results if the median of the oxygen content is used as the group divider suggesting that there might be a chance of more hurricanes if the oxygen content in the gulf is low.

However the data may not exactly meet the conditions of the t-test, so the results must be taken with a grain of salt.  I would say more data points would aid in a more significant conclusion.

However, a significant negative correlation does exist between the yearly average oxygen content of the gulf and the average strength of the hurricanes.

### Statistical Testing Summary

* There is a significant negative correlation between the yearly average gulf temperature and the yearly number of hurricanes affecting the gulf coast.

* Also, there is a significant negative correlation between the yearly average oxygen content of the gulf and the yearly average strength of the hurricanes that affect the gulf coast.

* Moreover, the coast length of each state is a huge determining factor in the number of hurricanes that will hit particular states in a year.

* No significant dividing temperature or oxygen content could be found with available data to give a boundary for higher chance of hurricanes and/or higher strengths for the year.

* More data is needed to provide a larger sample size and more statistical power.

****

## Machine Learning Model Performance

After looking at the data in the previous steps, I decided to group the hurricane impacts per year into groups based on the number of hurricanes per year and the average category strength per year.

There are two main predictions that will be made:
1. The number of hurricanes severity for that year.
2. The average strength severity for that year.

**For lack of time I only did one prediction group (number of hurricanes for the year).  There is code written for two groups up until a point, then I notated where future work could be done.**

### Categories for the groupings

I chose to do groups intstead of linear regression techniques because I have strong suspicions that the model will always perform poorly if trying to predict the exact number of hurricanes per year or exact average strength per year.  Groups will allow for classification which I believe the model will have more success performing.  The impact is still similar in that we can still obtain quality information about the severity of the upcoming hurricane season.

Moreover, the scoring criterion for the models will be the classification report which includes precision, recall, and f1 scores for each class and an average for the scores.  Emphasis will be put on the f1 score as this score is best indicative in this case for a better model as I don't want too many false positives, and I also don't want too many false negatives.

1. Hurricanes per year:
    * No Impacts (0)
        * 0 hurricanes
    * Moderate (1)
        * 1-2 hurricanes
    * Severe (2)
        * 3+ hurricanes
	
2. Average strength per year:
    * Mild (0)
        * 0-1.5 average category ranking
    * Moderate (1)
        * 1.5-3 average category ranking
    * Severe (2)
        * 3-5 average category ranking
        
### Feature Engineering and Model Selection

1. Features
    * Since temperature was the only statistically significant trend, let's try just temperature as a feature first.
    * Try adding in the rest of the wod features into the model next.
        * The features will be averages for the time before the hurricane season (January 1st to May 31st) for that year.
    * Try adding in previous years ocean data as features.
        * The features will be averages for the time before the hurricane season (January 1st to May 31st) for the previous year.
    * Try adding in previous years number of hurricanes and average strength as features.
    
2. Models
    * Logistic Regression
    * SVM Classification
    * kNN

The 3 models were chosen above as they are three of the most popular and robust classification algorithms in use to date.

### Model Setup and Performance Results

The 3 models were each tested with the different features outlined above.  This created three by four or 12 instances.  Each case was run 5 times and the distribution of f1 scores was analyzed to judge model performance.
* The features were standardized before being implemented in the model.
* Randomized search cross validation was done in order to tune the model hyper parameters.  
	* Logistic Regression: C
	* SVC: C and gamma
	* kNN: k (Grid search cross validation)

**1. Logistic Regression Performance Summary**

The f1 scores and tuned hyperparameters for the different features are outlined below in *Table 10*, and in the boxplots shown in *Figure 23* below.

|Feature Set|f1 score|
|-----------|--------|
|Tempeature Only|f1 = 0.60<br>C = 373|
|All WOD Features|f1 = 0.50<br>C = 0.001|
|Above with last year's WOD features|f1 = 0.33<br>C = 0.001|
|Above with last year's # of hurricanes and average strength|f1 = 0.47<br>C = 2.74|

*Table 10: Logistic Regression performance results with different feature sets.*

<img width="700" alt="logreg feature test comparison" src="https://user-images.githubusercontent.com/23604099/39597943-00b542d0-4ee5-11e8-9394-be565272a01e.PNG">

*Figure 23: Boxplots of the f1 scores for testing different feature sets with Logistic Regression.*

Logistic Regression performed best with temperature as the only feature.  Model performance is pretty low overall.  For logistic regression with the features available, I would choose to run with temperature as the only feature.
    * More research into features need to be done in order to create a better performing model and/or more data is needed.
    
**2. Support Vector Machine Classification Performance Summary**

The average f1 scores and tuned hyperparameters for the different features are outlined below in *Table 11*, and in the boxplots shown in *Figure 24* below.

|Feature Set|f1 score|
|-----------|--------|
|Tempeature Only|f1 = 0.66<br>C = 22425<br>gamma = 0.04|
|All WOD Features|f1 = 0.60<br>C = 88<br>gamma = 0.008|
|Above with last year's WOD features|f1 = 0.49<br>C = 243<br>gamma = 0.0004|
|Above with last year's # of hurricanes and average strength|f1 = 0.27<br>C = 203<br>gamma = 0.00004|

*Table 11: SVC performance results with different feature sets.*

<img width="700" alt="svc feature test comparison" src="https://user-images.githubusercontent.com/23604099/39599574-26f26e6e-4eea-11e8-8c00-001494a87e3c.PNG">

*Figure 24: Boxplots of the f1 scores for testing different feature sets with SVC.*

SVM classification performed best with temperature as the sole feature.  Model performance is pretty low.  For SVC with the features available, I would choose to run with temperature as the only feature.
* More research into features need to be done in order to create a better model and/or more data is needed.
* Also, the model performed on average worse than logistic regression.

**3. k Nearest Neighbors Performance Summary**

The average f1 scores for the different features are outlined below in *Table 12*, and in the boxplots shown in *Figure 25* below.

|Feature Set|f1 score|
|-----------|--------|
|Tempeature Only|f1 = 0.71<br>k = 9|
|All WOD Features|f1 = 0.60<br>k = 11|
|Above with last year's WOD features|f1 = 0.38<br>k = 7|
|Above with last year's # of hurricanes and average strength|f1 = 0.37<br>k = 13|

*Table 12: kNN performance results with different feature sets.*

<img width="700" alt="knn feature test comparison" src="https://user-images.githubusercontent.com/23604099/39600316-5f88f4ee-4eec-11e8-808d-691de6b0eaca.PNG">

*Figure 25: Boxplots of the f1 scores for testing different feature sets with kNN.*

kNN performed best with temperature as the only feature.  Model performance is moderate.  For kNN with the features available, I would choose to run with temperature as the only feature.
* More research into features need to be done in order to create a better model and/or more data is needed.
* Also, the model performed on average better than logistic regression and svm classification.

### Machine Learning Performance Summary

* As of right now the best performing model is kNN (k=9) with only temperature as the sole feature in the model.
    * Average f1 score of 0.71 with a nice balance between precision and recall (0.8 and 0.75 respectively).
    * The model has the best performance when it comes to not making too many false positives at the expense of making false negatives and vice versa in each of the three categorical classes.
* There are too few data points and not enough statistically significant features to use in the models.
* In order to improve the various models, more research into features needs to be done, and more data collected.
    * However, the only way to collect more data is by waiting and recording.
    * Other features could be global weather trends for the year such as off the coast of africas where hurricanes start or the north atlantic current, etc.
    * Here are the features used by NOAA to predict hurricane activity but not landfall.
        1. The conducive conditions which were predicted in May for the Atlantic hurricane Main Development Region (MDR, which includes the tropical Atlantic and Caribbean Sea) are now present and are expected to persist through the peak months (August-October, ASO) of the hurricane season. These conditions include weaker vertical wind shear, weaker trade winds, more conducive wind patterns coming off of Africa, and a stronger west African monsoon.

        2. There is much higher confidence that ENSO-neutral conditions will persist through ASO, and that El Niño will not develop and suppress the season. 

        3. Warmer sea surface temperatures (SSTs) in the MDR are more likely to persist than key models had previously predicted.

        4. All models now predict a more active season than they did in May.

        5. Enhanced June-July activity in the tropical Atlantic (tropical storms Bret and Don) reinforces the expectation for an above-normal season. Historically, years with early-season activity in this region have a higher likelihood of being above-normal.
* Moreover, the activity (number of storms) in the atlantic system (includes Gulf of Mexico) tends to follow Poisson's Distribution quite well.

#### This is where future work could be done with alternative algorithms and modeling of category strength, etc.

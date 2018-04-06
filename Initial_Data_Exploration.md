In exploring the datasets created in the Data Wrangling step, I wanted to look into answering two main questions.
* Are there any factors of the ocean properties that trend well with the frequency of hurricanes?
* If so, what are they and how strong of an influence do they seem to have?
Moreover, I wanted to get a feel for the impacts that hurricanes have had over the years on the Gulf Coast of the United States.  This includes frequency, strength, and strength frequency for both the Gulf Coast as a whole and per state.

I feel the best way to illustrate the initial analysis results is in a Q and A format.

#### Q: How often and how many hurricanes impact the Gulf Coast of the United States?
#### A: A trend of 0-3 hurricanes per year is pretty steady throughout history with a few outlying years, most notably 2005 with 11 hurricane impacts along the gulf coast.  Zero hurricanes is the most seen amount throughout history. 90% of the years had 3 hurricanes or less with most years having 0 or 1 hurricane per year.
<img width="608" alt="hurricanes per year over time" src="https://user-images.githubusercontent.com/23604099/38057950-1c57f62e-32af-11e8-97ae-19672a6378ae.PNG">
<img width="553" alt="frequency of hurricane occurences per year" src="https://user-images.githubusercontent.com/23604099/38058000-4e885300-32af-11e8-8a53-886ecd1f10c0.PNG">
<img width="561" alt="cdf hurricanes per year" src="https://user-images.githubusercontent.com/23604099/38058034-6e2fee0c-32af-11e8-81fa-ea6330a7d25f.PNG">

#### Q: How strong are the hurricanes that impact the United States Gulf Coast, and with what frequency do the respective strengths impact the US Gulf Coast?  Hurricanes or ranked by a Category system from 1 to 5.  Category 1 is the weakest, and Category 5 is the strongest.
#### A: After reviewing the category data, it shows that the most frequent category is Category 1 and the least frequent is Category 5. Moreover, the most frequent occurence is for the Gulf Coast to have 1 Category 1 hurricane per year.
<img width="558" alt="frequency of categories per year" src="https://user-images.githubusercontent.com/23604099/38058464-ebd5b6b0-32b0-11e8-9bc4-da7db2552441.PNG">
<img width="343" alt="frequency of category occurences per year" src="https://user-images.githubusercontent.com/23604099/38058656-a09557f4-32b1-11e8-8e9d-ee63d0f528cf.PNG">

Now, I will look at the ocean data of the Gulf of Mexico averaged as a whole.  I will investigate whether there are any correlations between the ocean properties and the number of hurricanes that impacted the gulf coast.  Also, I will look into the correlations between ocean features and average strengths of the storms.

#### Q: What are the trends in the main gulf features over time (temperature, oxygen, phosphates, salinity, and silicates)?
#### A: It seems that most of the features have remained relatively steady besides temperature. There have been upsets in all the factors. Most interesting is that the average temperature for the gulf was much lower the year of 2005, the year of 11 hurricanes impact.  Dips in the temperature can be observed for every significant peak in the number of hurricanes.  All the features have low correlations with the ocean data with temperature being the strongest.  Also, there is a fair amount of positive correlation between salinity and temperature, and silicates and oxygen. There seems to also be some negative correlation between silicates and temperature and salinity and oxygen, and phosphates and salinity.

* Below are line plots of the features averaged over the entire Gulf of Mexico per year vs. the hurricanes per year.
<img width="468" alt="ocean_params_time" src="https://user-images.githubusercontent.com/23604099/38146476-28fe6f66-341c-11e8-9f37-52a28a5c4e3d.png">

* Next are Pearson correlation coefficients for each ocean feature and the number of hurricanes per year.

|Ocean Parameters|Correlations with Number of Hurricanes per Year|
|----------------|-----------------------------------------------|
|Oxygen|-0.139101|
|Phosphate|0.161184|
|Salinity|-0.214846|
|Silicate|0.122182|
|Temperature|-0.362684|

* Next I looked to see if there were any correlations between the ocean parameters.
<img width="294" alt="wod param corr" src="https://user-images.githubusercontent.com/23604099/38143368-3238b7b6-340e-11e8-8e32-0c54302091fc.PNG">

****

After looking at the gulf of Mexico as a whole, I thought it would be worthwhile to look into the breakdowns by state.

#### Q: Which states are impacted most and least often by hurricanes?
#### A: After review of cursory hurricanes by state data, it seems Florida and Louisiana are impacted the most with Alabama and Mississippi impacted the least. Florida is impacted by higher strength storms compared to the rest of the states, which are mostly affected by category 1 storms.  Moreover, the highest likelihood is to be impacted by 0 or 1 storms per year.
<img width="573" alt="hurricane state count" src="https://user-images.githubusercontent.com/23604099/38064628-fe578c8e-32cc-11e8-8443-92d3ea32feb5.PNG">
<img width="319" alt="state count frequency" src="https://user-images.githubusercontent.com/23604099/38064656-2bc108c6-32cd-11e8-871a-da6d5d85c024.PNG">
<img width="433" alt="categories by state" src="https://user-images.githubusercontent.com/23604099/38064675-4c6a6612-32cd-11e8-93e2-710679244217.PNG">
<img width="557" alt="hurricanes per year by state" src="https://user-images.githubusercontent.com/23604099/38144432-aed1b350-3412-11e8-99bb-3cd8ccfc20ed.PNG">

****

Upon the above simple initial questions, the data was seeming to show that certain states were always being impacted more frequent and with stronger storms.  I decided to dig in deeper into different features of the states to see if they showed any interesting trends and or correlations.

#### Q: What trend or impact does coast length have on number of hurricane impacts per year?
#### A: After looking into hurricane counts vs. coast length for each state it seems that their is a positive correlation between the two quantities. This suggest that states with larger coast lines tend to be hit more often than states with smaller coast lines. This may just be circumstantial to other factors such as sea temperature, sea floor depth, etc. along those coast lines.
<img width="566" alt="coast length vs hurricanes" src="https://user-images.githubusercontent.com/23604099/38143248-a4f37fd0-340d-11e8-8012-79d02c56344e.PNG">

#### Q: How do the ocean properties and hurricane impacts change over time by location? Broken down by state.
#### A: 
* #### After looking at the trends visually it seems the correlations are similar to the whole gulf averaged data above.
![alabama_ocean_params_time](https://user-images.githubusercontent.com/23604099/38427999-b7f12b34-3988-11e8-85da-99c816a5b0da.png) 


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
#### A: It seems that most of the features have remained relatively steady besides temperature. There have been upsets in all the factors. Most interesting is that the average temperature for the gulf was much lower the year of 2005, the year of 11 hurricanes impact.  Dips in the temperature can be observed for every significant peak in the number of hurricanes.  All the features have low correlations with the ocean data with temperature being the strongest.

* Below are line plots of the features averaged over the entire Gulf of Mexico per year vs. the hurricanes per year.

<img width="490" alt="gulf of mexico oxygen line" src="https://user-images.githubusercontent.com/23604099/38120786-f2a1ad4e-3398-11e8-8080-3bf298a5fe7a.PNG">
<img width="445" alt="gulf of mexico phosphate line" src="https://user-images.githubusercontent.com/23604099/38120798-184929b4-3399-11e8-8aa3-6e9e6fc47204.PNG">
<img width="450" alt="gulf of mexico salinity line" src="https://user-images.githubusercontent.com/23604099/38120831-4959b26c-3399-11e8-942c-ff53c22c4616.PNG">
<img width="434" alt="gulf of mexico silicate line" src="https://user-images.githubusercontent.com/23604099/38120843-646c3ca0-3399-11e8-9130-e79b3447b8fd.PNG">
<img width="453" alt="gulf of mexico temp line" src="https://user-images.githubusercontent.com/23604099/38120870-888a5c5c-3399-11e8-83dc-578949303d3e.PNG">

* Next are the scatter plots and Pearson correlation coefficients for each ocean feature and the number of hurricanes per year.

<img width="468" alt="oxy hurricane gulf of mexico scatter" src="https://user-images.githubusercontent.com/23604099/38120915-f27abee0-3399-11e8-9dae-7f4c56830684.PNG">

****

After looking at the gulf of Mexico as a whole, I thought it would be worthwhile to look into the breakdowns by state.

#### Q: Which states are impacted most and least often by hurricanes?
#### A: After review of cursory hurricanes by state data, it seems Florida and Louisiana are impacted the most with Alabama and Mississippi impacted the least. Florida is impacted by higher strength storms compared to the rest of the states, which are mostly affected by category 1 storms.
<img width="573" alt="hurricane state count" src="https://user-images.githubusercontent.com/23604099/38064628-fe578c8e-32cc-11e8-8443-92d3ea32feb5.PNG">
<img width="319" alt="state count frequency" src="https://user-images.githubusercontent.com/23604099/38064656-2bc108c6-32cd-11e8-871a-da6d5d85c024.PNG">
<img width="433" alt="categories by state" src="https://user-images.githubusercontent.com/23604099/38064675-4c6a6612-32cd-11e8-93e2-710679244217.PNG">

****

Upon the above simple initial questions, the data was seeming to show that certain states were always being impacted more frequent and with stronger storms.  I decided to dig in deeper into different features of the states to see if they showed any interesting trends and or correlations.

#### Q: What trend or impact does coast length have on number of hurricane impacts per year?
#### A: After looking into hurricane counts vs. coast length for each state it seems that their is a positive correlation between the two quantities. This suggest that states with larger coast lines tend to be hit more often than states with smaller coast lines. This may just be circumstantial to other factors such as sea temperature, sea floor depth, etc. along those coast lines.
<img width="565" alt="coast length vs hurricanes" src="https://user-images.githubusercontent.com/23604099/38064721-9ff90cac-32cd-11e8-8c14-66af686fec23.PNG">




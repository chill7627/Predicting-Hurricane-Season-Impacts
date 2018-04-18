# Predicting Hurricane Season Impacts
  **Milestone Report**
  
  **18th April 2018**
  
  **Christopher Seth Hill**

****

### OVERVIEW

Every year the Gulf Coast is impacted by numerous tropical weather systems.  The deadliest and most damaging of these being hurricanes.  A single hurricane can cause damages of $50 billion plus and rack up a hefty death toll.  Currently, Gulf Coast states could use a simple way to assess or forecast potential impacts for the upcoming hurricane season.  Having a predictive model for hurricane season impacts would aid states in allocating resources and budgeting for probable damages.

****

### INTERESTED PARTIES

The federal, state, and local governments, especially those along the Gulf Coast, would be interested in a way to predict the impact of upcoming hurricane seasons.  This would allow the governments and citizens to better gauge the necessity for hurricane damage mitigating resources.  More importantly, this would also help in decreasing the loss of life associated with hurricanes.  In addition, this would also aid in budgeting, preventing over or under budgeting.

****

### DATA

#### Data Wrangling World Ocean and Historical Hurricane Data
* Code can be found [here](https://github.com/chill7627/Predicting-Hurricane-Season-Impacts/blob/master/Hurricanes_Data_Wrangling.ipynb).

**1. National Oceanic and Atmospheric Administration World Ocean Database**

* **Background Information and File Selection**
    
The World Ocean Database (WOD) is a massive data collection and storage project supported by NOAA.  It houses copious amounts of  data from the world's oceans.  Data is stored in datasets based on the various instruments used to collect the data.  The data contains depth profile information about ocean properties such as: 
      * Temperature
      * Salinity
      * pH
      * Pressure
      * Phosphates
      * Silicates
  
The methods of Data Acquisition employed to gather the data are outlined in the table below:

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


Data is organized in files that I selected based on geographical location.  I chose only the areas relevant for the Gulf of Mexico.  The locations were based on 10&deg; grid blocks.  The selection map is presented below:

![alt text](https://www.nodc.noaa.gov/OC5/WOD/wmo_world13.png "Sectioned World Map")

I selected 7208 and 7209 locations.  The next step was to determine which instrument data that I wanted to use.  I landed on the OSD data as it seemed to have the most properties in one dataset.  More detailed information about the data and how it is organized can be found in the [WOD Documentation](https://www.nodc.noaa.gov/OC5/WOD/docwod.html).  If later steps find that more data is needed from the other instruments, I will merge this data into the data already wrangled.

* **File Download and Incorpation into Pandas DataFrames**

The files are zipped and in a proprietary ascii format.  The files contain multiple WOD profiles that each have depth profile data at a specific latitude, longitude and timestamp.  I used the [wodpy package](https://pypi.python.org/pypi/wodpy/1.5.0) which was created to decode the profile data contained in the WOD files.  I used wodpy to create dataframes from the profiles.  The resulting dataframes contain the salinity, temperature, etc data in columns with standard depths being the index, and the location and time is stored as attributes for that particular dataframe.  Moreover, I iterated over each profile in the file and stored the resulting dataframes in a list.  I then created an empty master dataframe and appended the dataframes in the list to the master dataframe.

In examining the dataframes being appended to the master, I noticed there were empty dataframes.  I chose to check to see if a dataframe was empty before appending to the master in order to filter out the empty dataframes.

* **Exploring, Manipulating, and Cleaning**
    
Now that I had one master data frame with the relevant data, I could begin to clean, organize, and manipulate the data.  Firstly, the time information was separated into multiple columns for month, day, year, and time.  I created one column with a datetime object and sorted the dataframe based on these values.  I also dropped the redudant columns from the dataframe.

Next, the latitude and longitude data were in separate columns.  I zipped them together into tuples in one column to represent a combined (lat, lon) location.  I then dropped the redundant longitude and latitude columns from the dataframe.

Then, I made a decision to drop all data predating 1960 as this was when the first satellite was launched to track weather http://www.aoml.noaa.gov/hrd/tcfaq/J6.html.

After looking at the latitude and longitude data more closely, it was inconsistent with rounding and decimal places.  I chose to round these values to the nearest degree.  I then created a multiIndex for the dataframe based on location and date.  Next, I resampled each location by monthly averages.  

Upon inspection, the data was mostly missing for the pH and pressure columns, so I chose to drop them from the dataframe.  Moreover, I filled NaN values by first filling NaNs by location averages.  Then, the remaining NaNs were filled by back filling.  

![wod data histogram](https://user-images.githubusercontent.com/23604099/36941433-162e8176-1f29-11e8-8cdc-01042197e9b0.png)

After performing EDA on the dataframe, I decided to leave the high data points in the dataframe as they seemed they could be real.  The following illustrate the overall data:

![boxplots wod data](https://user-images.githubusercontent.com/23604099/36941472-012e9ba2-1f2a-11e8-9db3-f71236c21324.png)
![line plots wod data](https://user-images.githubusercontent.com/23604099/36941476-0c368f50-1f2a-11e8-955c-8471e35ed4c7.png)


**2. Historical Hurricane Data**
  
* **Background Information and File Selection**
    
The Historical Hurricane Data lists the hurricanes per year,  their categories, and their landfall locations.  I found this data in wiki tables separated by state in the [List of United States Hurricanes](https://en.wikipedia.org/wiki/List_of_United_States_hurricanes) wiki page.  

In order to wrangle the data into python, I had to scrape the wiki page's tables.  I used the [wikipedia package](https://pypi.python.org/pypi/wikipedia).  Below is an example of a dataframe straight from the wiki table:

<img width="760" alt="sample raw wiki table" src="https://user-images.githubusercontent.com/23604099/36947617-b9c71e2e-1f9c-11e8-8c30-022f90e0aa77.PNG">

The tables from the wiki page have several issues that need to be cleaned up.  First I fixed the fact the tables were set up in a split column fashion.  I had to put the data into one long dataframe.  Also, the date was split into two columns.  I fixed this by using datetime.strptime() method.  In addition, I removed any blank rows that existed in the dataframes.  The result is below: 

<img width="368" alt="post datetime and concat hurricane data" src="https://user-images.githubusercontent.com/23604099/36947997-ed6d0f36-1fa1-11e8-8568-52fe0d232b13.PNG">

Next steps included:
* Removing any special [notes] in the category column.
* Renaming the remaining columns.
* Replacing special entries in quotations in the named column with "Unnamed".
* Adding a corresponding state label for each dataframe entry.
* Removing any entries pre-dating 1960 from the dataframe.

The result is below:

<img width="283" alt="final hurricane df" src="https://user-images.githubusercontent.com/23604099/37010989-15f23f7e-20bc-11e8-8c18-3d3e6a8ec072.PNG">



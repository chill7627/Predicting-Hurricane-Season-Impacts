# Data Wrangling World Ocean and Historical Hurricane Data
****
## National Oceanic and Atmospheric Administration World Ocean Database
### Background Information and File Selection  
The World Ocean Database (WOD) is a massive data collection and storage project supported by NOAA.  It houses copious amounts of data from the world's oceans.  Data is stored in datasets based on the various instruments used to collect the data.  The data contains depth profile information about ocean properties such as: 
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
****
### File Download and Incorpation into Pandas DataFrames
The files are zipped and in a proprietary ascii format.  The files contain multiple WOD profiles that each have depth profile data at a specific latitude, longitude and timestamp.  I used the [wodpy package](https://pypi.python.org/pypi/wodpy/1.5.0) which was created to decode the profile data contained in the WOD files.  I used wodpy to create dataframes from the profiles.  The resulting dataframes contain the salinity, temperature, etc data in columns with standard depths being the index, and the location and time is stored as attributes for that particular dataframe.  Moreover, I iterated over each profile in the file and stored the resulting dataframes in a list.  I then created an empty master dataframe and appended the dataframes in the list to the master dataframe.

In examining the dataframes being appended to the master, I noticed there were empty dataframes.  I chose to check to see if a dataframe was empty before appending to the master in order to filter out the empty dataframes.
****
### Exploring, Manipulating, and Cleaning
Now that I had one master data frame with the relevant data, I could begin to clean, organize, and manipulate the data.  Firstly, the time information was separated into multiple columns for month, day, year, and time.  I created one column with a datetime object and sorted the dataframe based on these values.  I also dropped the redudant columns from the dataframe.

Next, the latitude and longitude data were in separate columns.  I zipped them together into tuples in one column to represent a combined (lat, lon) location.  I then dropped the redundant longitude and latitude columns from the dataframe.

Then, I made a decision to drop all data predating 1960 as this was when the first satellite was launched to track weather http://www.aoml.noaa.gov/hrd/tcfaq/J6.html.

After looking at the latitude and longitude data more closely, it was inconsistent with rounding and decimal places.  I chose to round these values to the nearest degree.  I then created a multiIndex for the dataframe based on location and date.  Next, I resampled each location by monthly averages.  

Upon inspection, the data was mostly missing for the pH and pressure columns, so I chose to drop them from the dataframe.  Moreover, I filled NaN values by first filling NaNs by location averages.  Then, the remaining NaNs were filled by back filling.  

After performing EDA on the dataframe, I decided to leave the high data points in the dataframe as they seemed they could be real.  The following illustrate the overall data:

[[C:/Users/sethh/OneDrive/Springboard/Capstone Project 1/wod data histogram]]


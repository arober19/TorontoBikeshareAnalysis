Project Brief

Objectives  
  Analysis
 - Tabulate bike share usage by hour of day in descending order
 - Show seasonal variance of most used bike share locations
 - Identify the 3 most trafficked routes, and identify any seasonal variance in them
 - Calculate the total revenue of extra usage fees by quarter
 - Store all code in a private GitHub repository
 - Provide a database of all data created and cleaned for this exercise
  
  Visualization
 - Create a dashboard using at least one of the tables in Section 3.1.
 - Provide a link to an online version of the dashboard. 
 - Dashboard must include: 
     1. One table from steps 3.1.1 - 3.1.4
     2. Filter for date
     3. Graphical visualization of data
  
  Geographic Visualization
- Map of all bike share locations
- Map identifying 10 most trafficked stations and including linear
representation for the 3 most trafficked routes

Current State
The bikeshare data ranging from the 3rd Quarter of 2016 to the 4th Quarter of 2018, came in the form of 10 csv files amounting to over 4 million rows across 9 columns. This included features such as the trip start and stop times, trip duration, trip id, station names and ids as well as the user type.

Additional latitude and longitude data was required in order to map the bike station locations therefore gathering data from the bikeshare api endpoint was necessary to make the coordinates to station ids.

Data Preparation
Intially, it was clear the datetime formats posed an issue with as many as 6 different configurations spanning each quarter. That is why the trip_start_time and trip_stop time columns were converted to datetime objects before merging all datasets into a single dataframe. Converting all datetime formats was particularly troublesome with regard to the 4th quarter dataset for 2016. Datetime formats were found to switch back and forth often all the way throughout the file and required string specific string manipulations. After merging everything together and looking at the NaN values, there were no full rows of NaN values so nothing worth deleting but of the 10 dataset 6 of them were missing station ids causing about 1.6 millions NaN values. This also posed a problem of matching the latitude and longitude coordinates to the dataframe by using the data from the BikeShare API. Matching the coordinates with the station names had to be executed however there could be many different variations of a name listed. For example, "Wellesley St E / Yonge St (Green P)" and "Wellesley St E / Yonge St Green P" have the same station id but not the same station name, not exactly anyways. To solve this issue similar names had to be converted to the same string and this was done by using the fuzzy ratio which score the similarity between words and if the score was 80 of higher then the name from the csv data would be converted to the name from the API data. Following that, after looking at the distribution of trip durations seeing that the top 2 most common durations were 26 and 27 seconds, I decided to get rid of rows with trip durations less than one minute my reasoning being that it be a false trip with not enough time to get to the next nearest bike station. Lastly, an outlier was deleted which gave a date with a year of 2000.

Feature Engineering


Table Outputs & Aggregations

Results


Conclusions



Future Work

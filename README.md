# **Project Brief**
### **Aidan Robertson      -     Friday April 10th 2020**
## **Objectives:**  
  **Analysis**
 * Tabulate bike share usage by hour of day in descending order
 * Show seasonal variance of most used bike share locations
 * Identify the 3 most trafficked routes, and identify any seasonal variance in them
 * Calculate the total revenue of extra usage fees by quarter
 * Store all code in a private GitHub repository
 * Provide a database of all data created and cleaned for this exercise
  
  **Visualization**
 * Create a dashboard using at least one of the tables in Section 3.1.
 * Provide a link to an online version of the dashboard. 
 * Dashboard must include: 
     1. One table from steps 3.1.1 - 3.1.4
     2. Filter for date
     3. Graphical visualization of data
  
  **Geographic Visualization**
* Map of all bike share locations
* Map identifying 10 most trafficked stations and including linear
representation for the 3 most trafficked routes

## **Current State:**

The bikeshare data ranging from the 3rd Quarter of 2016 to the 4th Quarter of 2018, came in the form of 10 csv files amounting to over 4 million rows across 9 columns. This included features such as the trip start and stop times, trip duration, trip id, station names and ids as well as the user type.

Additional latitude and longitude data was required in order to map the bike station locations therefore gathering data from the bikeshare api endpoint was necessary to make the coordinates match to the station ids.

## **Data Preparation:**

Intially, it was clear the datetime formats posed an issue with as many as 6 different configurations spanning all quarters. That is why the trip_start_time and trip_stop time columns were converted to datetime objects before merging all datasets into a single dataframe. Converting all datetime formats was particularly troublesome with regard to the 4th quarter dataset for 2016. Datetime formats were found to switch back and forth often all the way throughout the file and required specific string manipulations. 

After merging everything together and looking at the NaN values, there were no full rows of NaN values so nothing worth deleting but of the 10 dataset 6 of them were missing station ids causing about 1.6 millions NaN values. This also posed a problem of matching the latitude and longitude coordinates to the dataframe by using the data from the BikeShare API. Matching the coordinates with the station names had to be executed however there could be many different variations of a name listed. For example, "Wellesley St E / Yonge St (Green P)" and "Wellesley St E / Yonge St Green P" have the same station id but not the same station name, not exactly anyways. To solve this issue similar names had to be converted to the same string and this was done by using the fuzzy ratio, which scores the similarity between words and if the score was 80 or higher then the name from the csv data would be converted to the name from the API data. 

Following that, after looking at the distribution of trip durations seeing that the top 2 most common durations were 26 and 27 seconds, I decided to get rid of rows with trip durations less than one minute my reasoning being that it be a false trip with not enough time to get to the next nearest bike station. Lastly, an outlier was deleted which gave a date with a year of 2000.

## **Feature Engineering:**

In order to look at the data as a result of the date and time, new columns were created for the Year, Quarter, Month, Day, Day of the week and Hour. An extra fees column was created in order to determine the sum of extra fees by quarter. Finally a route id column was made with the combination of the start station id and the stop station id. The resulting dataframe contained 3.95 million rows and 22 columns.

## **Table Outputs & Aggregations:**

Using groupby and sorting to find out the most used hours of the day, it was found unsurprisingly that 5 pm, 6pm and 4 pm were the busiest hours along with 8 am as the 4th busiest hour explaining rush hour, while the 5th busiest hour was at noon. Potentially due to lunch time strolls. 

Next, a table was created showing the seasonal variance for the most used bike stations. Taking the 10 most used stations and grouping their usage by quarter, you can clearly see the same seasonal trends in the data. The 3rd quarter is highest and 1st quarter is lowest while the 2nd and 4th quarter are closer in number, however the 2nd quarter generally falls short of the 4th. 

Making a table to determine the top 3 most used routes and their seasonal variance. The top 3 routes turned out to be, Yonge and Wellesley to Sherbourne and Wellesley, Front St and Blue Jays Way to Union Station and lastly Bathurst and Queens Quay to York and Queens Quay. Their seasonal variance show similar trends as were mention previously. 

The final table output was looking at total revenue from extra usage fees by quarter and this total across all quarters came to $1,429,748.00. The 3rd quarter brought in the most money with 2nd, 4th and 1st quarters bringing in money accordingly.

## **Postgres Database:**

After exporting the data to a csv, an RDS postgres instance was deployed on AWS using the free tier service.
It was connected to through the colaboratory notebook using this code:

```
pip install psycopg2

import psycopg2
connection = psycopg2.connect(
    host = 'database-1.cglpfnjcmmha.us-east-2.rds.amazonaws.com',
    port = 5432,
    user = 'postgres',
    password = 'bikeshare',
    database='torontobikeshare'
    )
cursor=connection.cursor()
```

Following that a table was created in the database following the structure of the exported bikeshare dataframe. 
The schema can be viewed using this code:

```
#getting schema info
sql = """
SELECT "table_name","column_name", "data_type", "table_schema"
FROM INFORMATION_SCHEMA.COLUMNS
WHERE "table_schema" = 'public'
ORDER BY table_name  
"""
pd.read_sql(sql, con=connection)
```

Now the data is loaded in and can be queried like this:

```
#querying
sql = """
SELECT *
FROM bikedata
limit 5
"""
pd.read_sql(sql, con=connection)
```

## **Tableau Story:**

As requested a dashboard was created on Tableau. The first page visualizes usage based on hour of the day, user type and over the time period of the whole dataset which can be filtered by the date. The second page of the dashboard displays the most used bike stations and routes, the most common occuring extra charge fee amounts and a heatmap of the bike stations in Toronto varying in color and size depending on the amount of usage which can also be filtered by the date.

The link for the tableau dashboard can be found here: https://public.tableau.com/views/TorontoBikeshareDataAnalysis/Story1?:display_count=y&:origin=viz_share_link

## **QGIS Map:**

As the last deliverable, a geographical visualization was made using QGIS 3.4 software. Using the latitude and longitude points to map all the bikeshare locations and also indicate the 10 most popular stations as well as the 3 most popular routes resulted in an interesting visual. What was also interesting is that the 3 most used routes all go east.

![TorontoBikeshareStationsMapping](https://user-images.githubusercontent.com/54183001/78968168-ee1f3e80-7ad1-11ea-9a11-a29913ee965a.jpg)

## **Future Work:**

* Determining the number of bikes being docked at a station at any given time, this would help with the distribution of bikes to different locations and knowing how many will be needed, this can be accomplished by utilizing more of the data from BikeShare API endpoint
* Time Series Analysis on Usages
* Predicting usage rates for new bike station locations

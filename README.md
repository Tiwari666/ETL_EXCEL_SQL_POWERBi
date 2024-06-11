# Building a Data Analysis Project:
A) Create a Database-Extracting data from the excel to SQL

B) Combining the Data from the three different tables--Transform--This merging of data from different sources transforms the separate datasets into a unified view.

i) Query and analyze data with SQL

C) Integrate Power BI with a Database--Loading data from one 

D) Create Data Visualizations Using Power BI --Business Intelligence



# A) Create a Database-Extracting data from the excel to SQL:

# Steps:

i) Open SQL Server Management Studio

ii) Copy the Server Name for later use, then click on Connect to get the SSMS window.

iii) Right click on "Databases" and select "New Database" from the drop down.

iv) Assign a Database Name in New Database window and click OK.

v) Expand the Databases to verify that a new database is created.

vi) In the Databases panel, right-click on the newly created database and select "Import Data" from "Task" drop down list.

vii) In the Import Data dialog box, select the data source that you want to import from.

viii) Browse data file or enter path.

ix) Choose the excel version you want to use and click Next.

x) Select a destination and verify the server and database name-write down the same new database name here.

xi) Select tables that you want to import.

Note: if you get the following error, please folllow the following video to fix it:

"The 'Microsoft.ACE.OLEDB.16.0' provider is not registered on the local machine" Error in importing process of xlsx to a sql server."

Link: https://www.youtube.com/watch?v=gjykOuT_xwQ

xii) Click on "Run immediately" to import  all the data  into the environment.

xiii) We can now view the data in the new database by expanding the Tables node in the Databases panel.

# B)  Combining the Data from the three different tables

To combine the data from the three tables, let's simply use the UNION operator among these commands.

select * from dbo.['2018$']

union

select * from dbo.['2019$']

union

select * from dbo.['2020$']


# EDA IN SQL---------------------------------------------------------

## Q1: Is our hotel revenue growing yearly?

To answer this question, we need to first create a single temporary table "hotels" that can combine all the data using following code for easier access and analysis.

In our dataset we don’t have revenue. However, we have adr (Average Daily Rate), stays_in_week_nights, and stays_in _weekend_nights. So, we will create a new column revenue by using the data of these three columns as follows.



with hotels as(
select * from dbo.['2018$']
union
select * from dbo.['2019$']
union
select * from dbo.['2020$'])

select * into #hotels_temp from hotels;

-------------------------------------------


select (stays_in_week_nights + stays_in_weekend_nights) * adr as revenue 
from #hotels_temp;


--------------------------------------------------
select 
arrival_date_year,
(stays_in_week_nights + stays_in_weekend_nights) * adr
as revenue from #hotels_temp

-----------------------------------------------GROUP BY arrival_date_year
------- calculating the sum of revenue while grouping the data by year.
SELECT 
arrival_date_year,
SUM((stays_in_week_nights + stays_in_weekend_nights) * adr)
as revenue 
FROM #hotels_temp
GROUP BY 
arrival_date_year;
-------------------------------------------ROUNDING RESULTS
SELECT 
    arrival_date_year,
    ROUND(SUM((stays_in_week_nights + stays_in_weekend_nights) * adr), 2) AS revenue 
FROM 
    #hotels_temp 
GROUP BY 
    arrival_date_year;
	----------------------------------------------GROUP BY arrival_date_year,hotel
SELECT 
    arrival_date_year,hotel,
    ROUND(SUM((stays_in_week_nights + stays_in_weekend_nights) * adr), 2) AS revenue 
FROM 
    #hotels_temp 
GROUP BY 
    arrival_date_year,hotel;
	-----------------------------------------
SELECT 
    arrival_date_year,
    hotel,
    ROUND(SUM((stays_in_week_nights + stays_in_weekend_nights) * adr), 2) AS revenue 
FROM 
     #hotels_temp 
GROUP BY 
    arrival_date_year, hotel
ORDER BY 
    arrival_date_year;

---------------------------- READING A NEW TABLE--COLUMN NAME =market_segment
SELECT * FROM  dbo.market_segment$;
----READING '2018$' TABLE--COLUMN NAME =market_segment, MEAL
select * from dbo.['2018$'];
---READING '2018$' TABLE--COLUMN NAME =MEAL
SELECT * FROM  [dbo].[meal_cost$];
---So, we can join the table: #hotels_temp--2018,2019 and 2020-- with the market_segment$ and meal_cost$




------------------NOW, JOINING THE TABELS:market_segment$ , #hotels_temp, AND  meal_cost$



SELECT *
FROM #hotels_temp AS H
LEFT JOIN dbo.market_segment$ AS MS
    ON H.market_segment = MS.market_segment
LEFT JOIN dbo.meal_cost$ AS MC
    ON MC.meal = H.meal;


-----------------------------



--------------------------------------------------------------
## Q.2: Should we increase our parking lot size?
--To answer this question, we need to focus on the required_car_parking_spaces and number of guests staying in the hotel.
--Calculate the average occupancy rate of your parking lot.
SELECT 
    AVG(required_car_parking_spaces) AS avg_parking_spaces_used
FROM 
    #hotels_temp;

---Determine the times or seasons when the parking lot is most crowded.
--This can help in understanding if there's a consistent need for more parking spaces.
SELECT 
    hotel,
	arrival_date_month,
    arrival_date_year,
    SUM(required_car_parking_spaces) AS total_parking_spaces_used
FROM 
    #hotels_temp
GROUP BY 
    hotel, arrival_date_month, arrival_date_year
ORDER BY 
    total_parking_spaces_used DESC;
	--------------------------------------------------------------
--Look for any trends or patterns in parking space usage over time. 
--This can help in predicting future demand.

SELECT 
    arrival_date_year,
    arrival_date_month,
    SUM(required_car_parking_spaces) AS total_parking_spaces_used
FROM 
     #hotels_temp
GROUP BY 
    arrival_date_year, arrival_date_month
ORDER BY 
    arrival_date_year, arrival_date_month;

---------------------------------------------
--Compare the average occupancy rate with the total capacity of the parking lot.
--If the occupancy rate is consistently high and nearing or exceeding capacity, it might indicate a need for expansion.

SELECT 
    -- Calculating the total number of records (rows) in the #hotels_temp table
    (SELECT COUNT(*) FROM #hotels_temp) AS total_records,
    -- Total number of records where parking spaces were used
    (SELECT COUNT(*) FROM #hotels_temp WHERE required_car_parking_spaces > 0) AS total_parking_records,
    -- The total number of records where parking spaces were not used
    (SELECT COUNT(*) FROM #hotels_temp WHERE required_car_parking_spaces = 0) AS total_non_parking_records;
   
---Alternative with the average records:
WITH Counts AS (
    SELECT 
        COUNT(*) AS total_records,
        SUM(CASE WHEN required_car_parking_spaces > 0 THEN 1 ELSE 0 END) AS total_parking_records,
        SUM(CASE WHEN required_car_parking_spaces = 0 THEN 1 ELSE 0 END) AS total_non_parking_records
    FROM 
        #hotels_temp
)
SELECT 
    total_records,
    total_parking_records,
    total_non_parking_records,
    -- Calculate the average number of records
    (total_parking_records + total_non_parking_records) / 2.0 AS avg_records
FROM 
    Counts;
----------------.....................................................................

# POWERBi start:
Insert

Shape

take a line and divide the canvas into 3 parts

click on "card"

search "revenue" in teh search bar

Drag and drop the "revenue" into the card and format it.

Similarly, get another card, drag and drop the "adr", format it and change it to the average value,clicking on the "field" drop-down arrow..
------

create a new measure called "Total Nights" just clicking on the "stays_in_week_nights" column, format it as usual.

Total Nights = sum(Query1[stays_in_week_nights]) + sum(Query1[stays_in_weekend_nights])

# How to format the "callable value" for "Average of Discount""

click on the name of the column

A new menu called "column tools" appear then choose % or o.oo as required.


# How to get the revenue over years?

--get a table

-Drag and drop the Revenue and Reservation Status Date

-click on the Reservation Status Date

--select the "Reservation Status Date" to change the date format

--now, choose the line graph to see the trends over time.

# How to filter out the unimportant data/column/date here?

-drag and drop the "Reservation Status Date" into the "filter on this page" under the "filter" pane.

--select the "advance filtering" from the drop down

--"show item when the value" 

--choose the required option from the drop-down menu

--"Apply filter"

# how to filter the result based on thr some feature, like country here? Connection of different results in cards using the slicer.

--choose the "slicer" from the visualizations

-drag and drop the "country" column into the "slicer".

--choose one of the options from the slicer, it will change results in different cards accordingly.

# Similarly, create the slicer for the "hotel" column as well.

Please do not forget to drag and drop the "hotel" column into the line chart graph to make it workable.

# If visualization menu is missing?

-file

- option and setting

- options

- preview features

- 
- ok
- clck on the "new visuals" in the middle of the page to see all the visualizaation tools.
- 

- 
------------------
# To see the trends of each KPIs' parameters,

--copy and paste the line graph underneath each respective KPIs'

--Make sure to off all the legend, title and unnecesay items.

--Take of the Y-axis values and replace the y axis column with adr, total noght and discount variaables.

-------

# Should we increase the parking lot?

-copy and paste one of the cards and make a new card for the "required_car_parking_spaces"

-drag and drop the "required_car_parking_spaces" into the new card.

-











